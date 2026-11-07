# Candy Store

Loja virtual em HTML, CSS e JavaScript puro, com Supabase (login, banco de dados e permissões) e publicação pelo GitHub Pages.

## Estrutura

```
index.html            a loja inteira (visual, lógica e configuração num arquivo só)
supabase/schema.sql   tabelas, permissões (RLS), função de pedido e produtos iniciais
```

## 1. Configurar o Supabase

1. Crie uma conta em supabase.com e um **New project**.
2. Abra **SQL Editor > New query**, cole todo o conteúdo de `supabase/schema.sql` e clique em **Run**.
3. Vá em **Authentication > Providers > Email** e **desligue "Confirm email"**. Assim quem cria a conta já entra direto. Se deixar ligado, cada cliente precisará confirmar o e-mail antes do primeiro login.
4. Vá em **Project Settings > API**. Copie a **Project URL** e a chave **anon public** para o bloco **CONFIGURAÇÃO** no começo do arquivo `index.html` (procure por `SUPABASE_URL`).
   Nunca use a chave `service_role` no site.
5. Em **Authentication > URL Configuration**, coloque em *Site URL* o endereço do seu GitHub Pages (passo 3).

## 2. Criar a conta do administrador (sem expor a senha)

A senha não fica em nenhum arquivo do projeto. Faça assim:

1. Publique o site (passo 3).
2. Clique em **Entrar > Criar conta** e cadastre:
   - Nome: Lucas Apollo da Silveira
   - Turma: qualquer uma
   - E-mail: lucasapollosilveira@gmail.com
   - Senha: a sua (você mesmo digita no formulário)
3. No Supabase, abra o **SQL Editor** e rode:

```sql
update public.profiles set is_admin = true
where id = (select id from auth.users where email = 'lucasapollosilveira@gmail.com');
```

4. Saia e entre de novo. O item **Painel** aparece no cabeçalho.

A permissão de administrador fica no banco (`profiles.is_admin`) e é checada pelas políticas RLS. Mesmo que alguém altere o JavaScript no navegador, o banco recusa as operações de quem não é admin.
Como a senha inicial foi enviada em texto numa conversa, troque-a em **Authentication > Users** assim que possível.

## 3. Publicar no GitHub Pages

1. Crie um repositório no GitHub e envie todos os arquivos desta pasta.
2. Em **Settings > Pages**, escolha *Deploy from a branch*, branch `main`, pasta `/ (root)`, e salve.
3. Em alguns minutos o site fica em `https://SEU-USUARIO.github.io/NOME-DO-REPOSITORIO/`.

Pode enviar o `index.html` com a URL e a chave anon: elas são públicas por design.

## Como funciona

- **Sessão permanente:** o Supabase guarda a sessão no navegador e a renova sozinho. O cliente continua conectado depois de fechar e abrir o site.
- **Preços:** o pedido é criado pela função `create_order` no banco, que lê os preços da tabela de produtos. O navegador só envia produto e quantidade.
- **Pirulito:** vem cadastrado como **não visível**. No painel, em Produtos, marque "Visível" e salve.
- **Visível x Disponível:** *Visível* decide se o produto aparece para os clientes. *Disponível* desligado mostra o produto como "Indisponível", sem poder comprar.
- **Pix:** o pedido entra como "Aguardando pagamento" e você confirma manualmente em Pedidos. Não há confirmação automática.
- **Pagamento automático (futuro):** exige um provedor como Mercado Pago ou Asaas e uma Edge Function do Supabase guardando a chave secreta como *secret*. Isso não está incluído.
- **Carrinho:** fica salvo no navegador só por conveniência. Cadastros, produtos, configurações e pedidos ficam no banco.

## Checklist de testes (faça antes de divulgar)

Este projeto foi escrito sem acesso a um projeto Supabase real, então **não foi executado de ponta a ponta**. Teste:

1. Criar conta, sair, entrar. Fechar o navegador e abrir de novo (deve continuar conectado).
2. Mudar a turma na engrenagem.
3. Adicionar produtos, mudar quantidades e conferir os totais.
4. Finalizar com Pix (copiar a chave) e com dinheiro. Tentar uma data passada (deve ser bloqueada).
5. Entrar como admin: ver os pedidos, mudar a situação, editar preços, criar e excluir produtos, ativar o Pirulito, trocar a chave Pix.
6. Com uma conta de cliente, confirmar que o item Painel não aparece.
7. Abrir no celular.

## Se algo não funcionar

O site mostra um aviso amarelo no topo dizendo o que falta:

- **"Modo prévia"**: a URL e a chave do Supabase ainda não foram coladas no `index.html`. Nesse modo os produtos aparecem, mas não dá para entrar nem fazer pedidos.
- **"Não foi possível carregar os produtos"**: o `supabase/schema.sql` ainda não foi executado, ou a URL/chave estão erradas.
- Se a página aparecer sem cores, o `index.html` provavelmente não foi enviado inteiro ao GitHub. Ele é um arquivo único e não depende de outras pastas.
