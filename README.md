# Recebimento de Notas Fiscais · Nagumo

Importa o relatório de agendamento do Acrux, marca cada agenda como **recebida** ou **não recebida** e registra fornecedores recebidos **sem agenda**.
Os dados ficam no **Firebase (Cloud Firestore)**: o que um colaborador marca aparece na hora para todos os outros, em qualquer computador ou celular.
A página é publicada no **GitHub Pages**.

| Arquivo | Para que serve |
|---|---|
| `index.html` | A aplicação completa (telas, logos, lógica e as chaves do Firebase). |
| `firestore.rules` | Regras de segurança do banco. Ficam no console do Firebase; aqui é só a cópia. |

---

## 1. Criar o projeto no Firebase (uma vez só)

1. Acesse **https://console.firebase.google.com** com a conta Google da empresa e clique em **Adicionar projeto**.
   Nome sugerido: `conferencia-recebimento-nagumo`. O Google Analytics pode ficar **desativado**. Clique em **Criar projeto**.
2. **Banco de dados**: menu *Criação* > **Firestore Database** > **Criar banco de dados** >
   local **southamerica-east1 (São Paulo)** > **Iniciar no modo de produção** > **Criar**.
3. **Regras de segurança**: ainda no Firestore, aba **Regras**. Apague todo o texto, cole o conteúdo do arquivo
   `firestore.rules` e clique em **Publicar**.
4. **Chaves do app**: clique na engrenagem ⚙ > **Configurações do projeto** > em *Seus apps* clique no ícone **`</>`** (Web) >
   apelido `conferencia-recebimento` > **Registrar app** (não precisa marcar Hosting).
   Aparece um bloco `const firebaseConfig = { apiKey: "...", authDomain: "...", ... }`.
   Copie os valores para `app/firebase-config.js` (na pasta *Agendamento*) e gere a página de novo com
   `python app/gerar_html.py`. Ou envie o bloco para o Claude, que preenche e gera.

Não é preciso ativar o Authentication: o app não tem login nem pede nome; quem tem o link usa.

## 2. Publicar no GitHub Pages

1. Em **https://github.com** clique em **New repository**. Nome: `conferencia-recebimento`. Visibilidade: **Public**. **Create repository**.
2. Clique em **uploading an existing file**, arraste `index.html`, `firestore.rules` e `README.md` desta pasta e clique em **Commit changes**.
3. No repositório: **Settings** > **Pages** > *Source*: **Deploy from a branch** > Branch **main** e pasta **/ (root)** > **Save**.
4. Em 1 a 2 minutos o link aparece no topo dessa página, por exemplo `https://cpdnagumocd.github.io/conferencia-recebimento/`.
   Esse é o link que os colaboradores abrem (no computador ou no celular).

> Para atualizar a aplicação no futuro, envie o novo `index.html` pelo mesmo caminho (**Add file** > **Upload files**).

## 3. Uso no dia a dia

- **Quem importa o relatório**: menu **Importar relatório** > arraste o `.xls` do Acrux. As agendas aparecem na hora para todos.
  Importar o mesmo relatório de novo (ou duas pessoas ao mesmo tempo) não duplica nada.
- **Quem controla o recebimento**: tela **Recebimento**, botões **Recebido** / **Não recebido** em cada agenda, e **Recebido sem agenda** para cargas sem agendamento.
- Linhas alteradas por outra pessoa piscam em amarelo por um instante.
- O rodapé do menu mostra a conexão: **Online · tempo real**, **Enviando alterações…** ou **Sem conexão**.
  Sem internet, dá para continuar marcando: as alterações são enviadas quando a conexão voltar.
- Se duas pessoas alterarem o mesmo registro ao mesmo tempo, cada campo fica com a última alteração recebida
  (uma pessoa mudando o status e outra a observação não se atrapalham).

## Bom saber

- **Segurança**: sem login, quem tem o link consegue ver e alterar os dados. Não divulgue o link fora da equipe.
  As regras só aceitam gravações no formato do app.
- **Custo**: o plano gratuito do Firebase (Spark) é suficiente. Cada dia é um único documento, e o app acompanha em tempo real só os
  últimos 60 dias. Datas mais antigas aparecem pelo botão no fim da tela **Importar relatório**.
- **Backup**: o botão **Exportar Excel** baixa o recebimento do dia em planilha.
