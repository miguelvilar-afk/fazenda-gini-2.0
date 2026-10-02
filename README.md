# Fazenda Três Porquinhos

Loja fictícia de venda de leitões, feita para a atividade de segurança de aplicações web.
HTML, CSS e JavaScript puros — sem build, sem backend. Funciona em qualquer hospedagem estática, incluindo o GitHub Pages.

## Como publicar no GitHub Pages

1. Crie um repositório novo no GitHub (pode ser público).
2. Envie todo o conteúdo desta pasta para o repositório:
   ```
   git init
   git add .
   git commit -m "Primeira versão do site"
   git branch -M main
   git remote add origin https://github.com/SEU-USUARIO/SEU-REPOSITORIO.git
   git push -u origin main
   ```
3. No repositório, vá em **Settings → Pages**.
4. Em **Branch**, selecione `main` e a pasta `/ (root)`, depois clique em **Save**.
5. Depois de alguns minutos o site estará no ar em algo como:
   `https://SEU-USUARIO.github.io/SEU-REPOSITORIO/`
6. Compartilhe esse link com seu amigo para a fase de hacker ético.

## Contas de teste (já cadastradas)

| Usuário        | Senha      | Tipo     |
|----------------|------------|----------|
| admin          | admin123   | admin    |
| joao.criador   | joao2024   | cliente  |
| maria.compras  | maria2024  | cliente  |

## Cupons de desconto (para testar o carrinho)

`PORCO10`, `FAZENDA20`, `TESTEADMIN100`

## Sobre o "banco de dados" e o pagamento

O GitHub Pages só serve arquivos estáticos — não roda backend nem banco de dados de verdade.
Por isso, usuários, produtos, carrinho e pedidos ficam guardados em `js/app.js` e no
`localStorage`/`sessionStorage` do navegador. O pagamento é inteiramente simulado: nenhum dado
de cartão é enviado para lugar nenhum.

## Estrutura

```
index.html       → página inicial / destaques
loja.html         → catálogo completo, com filtros e busca
carrinho.html     → carrinho de compras e cupom de desconto
pagamento.html    → pagamento simulado
pedidos.html      → histórico de pedidos (próprios e, se souber o número, de terceiros)
login.html        → login
cadastro.html     → cadastro de novo usuário
perfil.html       → perfil de usuário (recebe ?id=N pela URL)
admin.html        → área administrativa
css/style.css     → estilos
js/app.js         → produtos, usuários, carrinho, pedidos, login e sessão
```

## Aviso

Este site foi criado de propósito com falhas de segurança, para uma atividade de ensino sobre
hacking ético. Não reaproveite esta base de código para um site real sem corrigir as falhas.
