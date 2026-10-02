# Gabarito das vulnerabilidades (uso pessoal — não suba isto para o GitHub, não mostre ao colega)

Use isto na Etapa 4 (correção) e para conferir se as falhas que seu amigo achar batem com
as que você inseriu de propósito. Cada item segue o modelo do documento da atividade:
sistema, tipo, local, descrição, impacto, correção.

---

### 1. Credenciais expostas no código-cliente
- **Local:** `js/app.js`, array `USERS`
- **Tipo:** Exposição de informações sensíveis / autenticação insegura
- **Descrição:** Usuários, e-mails e senhas em texto puro ficam visíveis a quem abrir o
  arquivo `app.js` (Ctrl+U ou aba "Fontes" do navegador).
- **Impacto:** Qualquer visitante lista todas as contas e senhas sem precisar tentar nada.
- **Correção:** Autenticação deveria acontecer num backend real, com senhas com hash
  (ex.: bcrypt) e nunca enviadas ao navegador.

### 2. Backdoor de desenvolvedor no login
- **Local:** `js/app.js`, função `login()`
- **Tipo:** Falha de autenticação
- **Descrição:** Usuário `admin` com senha em branco é aceito — um atalho de testes
  esquecido no código.
- **Impacto:** Qualquer pessoa entra como administrador sem saber a senha real.
- **Correção:** Remover qualquer atalho/bypass de autenticação antes de publicar.

### 3. Controle de acesso quebrado no painel admin
- **Local:** `admin.html`, função `carregarAdmin()` / `isAdminSession()`
- **Tipo:** Broken Access Control
- **Descrição:** A página só confere `localStorage.getItem('isAdmin') === 'true'`, algo
  que qualquer visitante pode definir manualmente no console
  (`localStorage.setItem('isAdmin','true')`) e recarregar a página.
- **Impacto:** Acesso total à lista de usuários (com senhas) e a todos os pedidos da loja.
- **Correção:** A permissão de admin precisa ser validada num servidor a cada requisição,
  nunca confiando em algo guardado no navegador do próprio usuário.

### 4. IDOR no perfil
- **Local:** `perfil.html?id=N`
- **Tipo:** Quebra de controle de acesso / IDOR
- **Descrição:** Mostra o perfil do `id` da URL sem checar se é o dono da sessão atual.
- **Impacto:** Vazamento de telefone, endereço e observações de qualquer usuário.
- **Correção:** Comparar o id pedido com o id da sessão autenticada no backend.

### 5. XSS refletido na busca da loja
- **Local:** `loja.html`, função `buscar()`
- **Tipo:** Cross-Site Scripting (XSS refletido)
- **Descrição:** O texto da busca é inserido via `innerHTML` sem escapar nada. Um payload
  como `<img src=x onerror="alert(document.cookie)">` é executado como código.
- **Impacto:** Em produção, permitiria roubar dados de sessão.
- **Correção:** Usar `textContent` ou escapar caracteres especiais antes de exibir.

### 6. Sessão não é totalmente encerrada no logout
- **Local:** `js/app.js`, função `logout()`
- **Tipo:** Falha de gerenciamento de sessão
- **Descrição:** `logout()` remove `loggedUser` mas esquece de remover `isAdmin`.
- **Impacto:** A sessão administrativa "sobrevive" ao logout.
- **Correção:** Limpar todos os dados de sessão no logout, não só uma parte.

### 7. Cupons de desconto expostos no código-cliente
- **Local:** `js/app.js`, objeto `CUPONS`
- **Tipo:** Exposição de informação / lógica de negócio no cliente
- **Descrição:** Todos os códigos de cupom (incluindo um de 100% de desconto,
  `TESTEADMIN100`) ficam visíveis no JavaScript, sem nenhuma validação no servidor.
- **Impacto:** Qualquer visitante lê o arquivo e aplica o maior desconto disponível.
- **Correção:** Validar cupons num backend, nunca embutir a lista de códigos no front-end.

### 8. Manipulação do valor pago (price tampering)
- **Local:** `pagamento.html`, campo oculto `#valor-a-pagar` e função `finalizarCompra()`
- **Tipo:** Validação inadequada de dados enviados pelo cliente
- **Descrição:** O total a pagar é calculado no navegador e colocado num campo
  `<input type="hidden">`. Na hora de confirmar, o site lê esse campo em vez de
  recalcular o total a partir do carrinho — então editar o campo pelo "Inspecionar
  elemento" (ou editar `localStorage.carrinho` diretamente) muda o valor registrado como pago.
- **Impacto:** Alguém fecha uma compra de qualquer valor pagando o que quiser.
- **Correção:** O preço final de uma compra nunca deve ser confiado a partir do que o
  cliente envia; o servidor precisa recalcular o total a partir do catálogo oficial.

### 9. IDOR no histórico de pedidos
- **Local:** `pedidos.html?pedido=N`
- **Tipo:** Quebra de controle de acesso / IDOR
- **Descrição:** O número do pedido é sequencial (1001, 1002...) e a página mostra os
  detalhes de qualquer pedido pelo número, sem checar se pertence a quem está logado.
- **Impacto:** Vazamento de endereço de entrega, itens comprados e valor pago de outros clientes.
- **Correção:** No backend, só retornar o pedido se `pedido.usuarioId === usuário_da_sessão`.

---

## Flags escondidas (sistema de pontuação tipo CTF)

| Flag | Onde está | Vulnerabilidade relacionada |
|---|---|---|
| `FLAG{COMENTARIO_HTML}` | Comentário HTML no topo de `index.html` | Exposição de informação em comentário |
| `FLAG{ACESSO_ADMINISTRATIVO}` | Bio do admin (via IDOR) **e** banner + console do navegador em `admin.html` após burlar o acesso | IDOR / Broken Access Control |
| `FLAG{CUPOM_SECRETO}` | Comentário acima do objeto `CUPONS` em `app.js` | Lógica de desconto exposta no cliente |
| `FLAG{PRECO_MANIPULADO}` | Aparece em `pagamento.html` só quando o valor pago enviado não bate com o valor real do carrinho | Price tampering |
| `FLAG{HISTORICO_PEDIDOS_EXPOSTO}` | Aparece em `pedidos.html?pedido=N` só quando o pedido visto não é do usuário logado | IDOR em pedidos |

As duas últimas só aparecem quando a falha é realmente explorada (não só encontrada) —
são um bom jeito de confirmar pro seu amigo que ele "ganhou o ponto" daquela vulnerabilidade.
Classifique a dificuldade/pontuação de cada uma conforme os critérios do professor.
