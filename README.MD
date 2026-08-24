# Obra em casa — controle dos serviços do Jonathan

Site de uma página só (`index.html`) que substitui a planilha. Os dados ficam no
Firebase Firestore, então tudo que você lançar no celular aparece na hora no
computador — e vice-versa.

## O que o site faz

- **Serviços** — lista com tempo estimado, tempo gasto, status e datas. O custo
  de cada serviço é calculado como `tempo × valor da hora`, usando o tempo gasto
  quando ele estiver preenchido e caindo para o estimado enquanto não estiver.
- **Materiais** — cada material retirado abate do saldo, e a linha mostra quanto
  sobrou depois daquela compra.
- **Resumo** — total gerado em serviços, total retirado em material e o saldo.
- No topo, a fita mostra quanto do crédito já virou material.

---

## 1. Criar o projeto no Firebase

1. Entre em <https://console.firebase.google.com> e clique em **Adicionar projeto**.
   Pode desligar o Google Analytics.
2. No menu lateral, **Criar > Firestore Database > Criar banco de dados**.
   Escolha o modo de produção e a região `southamerica-east1` (São Paulo).
3. Ainda no menu, **Criar > Authentication > Vamos começar** e ative o provedor
   **E-mail/senha**. Depois vá na aba **Users > Adicionar usuário** e cadastre:
   - e-mail: `viniciosv@gmail.com` (só identifica a conta; você não digita ele no site)
   - senha: a que você vai digitar no site
4. Em **Configurações do projeto > Seus apps**, clique no ícone `</>` (Web),
   registre o app e copie o objeto `firebaseConfig` que aparece.

## 2. Colar a configuração no site

Abra o `index.html`, procure o bloco no começo do `<script>` e troque pelos seus
dados:

```js
const firebaseConfig = {
  apiKey: "...",
  authDomain: "seu-projeto.firebaseapp.com",
  projectId: "seu-projeto",
  storageBucket: "seu-projeto.appspot.com",
  messagingSenderId: "...",
  appId: "..."
};
```

Essas chaves não são segredo — quem protege os dados são as regras do passo 3.

## 3. Regras de acesso

No Firestore, aba **Regras**, cole o conteúdo de `firestore.rules` trocando os
e-mails pelos seus. Só quem estiver nessa lista consegue ler ou gravar.

## 4. Publicar no GitHub Pages

```bash
git init
git add index.html README.md firestore.rules
git commit -m "Controle dos serviços da obra"
git branch -M main
git remote add origin https://github.com/SEU-USUARIO/obra-casa.git
git push -u origin main
```

No repositório: **Settings > Pages > Source: Deploy from a branch**, branch
`main`, pasta `/ (root)`. Em um minuto o site sobe em
`https://SEU-USUARIO.github.io/obra-casa/`.

O login por senha funciona em qualquer domínio, então não precisa mexer em
domínios autorizados.

## 5. Primeiro acesso

Digite a senha que você cadastrou no passo 1 e clique em **Carregar a lista da planilha** — os oito
serviços entram já com os tempos e datas que você tinha. Depois é só usar.

---

## Como os dados ficam guardados

| Coleção | Documento | Campos |
|---|---|---|
| `config` | `geral` | `valorHora` |
| `servicos` | um por serviço | `nome`, `local`, `tempoEstimado`, `tempoGasto`, `status`, `dataPrevista`, `dataConclusao`, `obs`, `ordem` |
| `materiais` | um por compra | `item`, `qtd`, `unidade`, `valorUnit`, `data`, `loja`, `servicoId`, `obs` |

Datas são texto no formato `AAAA-MM-DD`, que ordena certo sem depender de fuso.

## Rodar na sua máquina antes de publicar

Não abra o arquivo direto pelo disco (`file:///`); suba um servidor simples:

```bash
python3 -m http.server 8000
```

Depois acesse `http://localhost:8000`.

## Trocar a senha

Firebase Console > **Authentication > Users**, três pontinhos na linha da conta
> **Redefinir senha**. Não precisa alterar nada no site.
