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

- **Quem importa o relatório**: menu **Importar relatório** > arraste o `.xls` do Acrux. O relatório pode trazer **vários dias**;
  cada agenda entra na sua data e aparece na hora para todos.
- **Importar de novo não repete nada**: agendas iguais (fornecedor, nota, valor e volumes) são ignoradas. Se o valor ou os volumes de
  uma agenda mudaram, ela é atualizada e o que já foi marcado (status, horário, observação) é mantido. Agendas que saíram do
  relatório não são apagadas.
- **Quem controla o recebimento**: tela **Recebimento**, botões **Pendente** (amarelo) / **Recebido** / **Não recebido** em cada agenda, e
  **Recebido sem agenda** para cargas sem agendamento. Sem nenhum botão marcado, a agenda fica cinza, como **Aguardando entrega**;
  clicar de novo no botão marcado desmarca. Agendas de **dias anteriores** que ficaram em Aguardando entrega passam sozinhas
  para **Não recebido** ("Automático: dia encerrado sem recebimento"). A agenda com antecipação registrada mostra a tag
  **Antecipada** (verde) ou **Não antecipada** (vermelha), com as notas fiscais.
- **Cargas antecipadas**: registra as antecipações do CD 2 como na planilha *CONTROLE DE AGENDAMENTO* (fornecedor, item, notas fiscais, situação do
  contrato, unidade, volumes previstos e antecipados, cargas, tipo e situação da carga, valor). A lista **Previsão pela agenda** traz as
  agendas do dia: **Registrar** já preenche fornecedor, valor e volumes. **Importar planilha** lê a aba *Fornecedores com Contrato*
  (atualize todo dia) e, se você quiser, os lançamentos da aba *Agendamento CD 2*. O app trabalha só com o **CD 2**.
  Lançamento novo entra; linha alterada na planilha (mesma data, fornecedor e item) atualiza o lançamento que já existe,
  mantendo a observação feita no app; linha igual não se repete; linha apagada da planilha continua no app.
  Quem não está na lista conta como **sem contrato logístico**; no lançamento dá para trocar por acordo pontual ou compra por oportunidade.
- **Itens em ruptura** (tela Cargas antecipadas): **Atualizar lista** carrega a planilha base do dia (*Lista de Itens em Ruptura CPD*).
  A agenda do fornecedor que tem item com **score de priorização 90 ou mais** ganha a tag amarela **Priorizar** (na previsão e no
  Recebimento); clicar na tag mostra os itens. Sem coluna de score na planilha, vale a curva A.
- **Dashboard**: cargas agendadas (relatório do Acrux) × cargas antecipadas por dia, com resumos por situação do contrato,
  tipo e situação da carga, e o recebimento das agendas no período escolhido.
- **Dias anteriores**: as setas e a lista de datas mostram os dias com agenda; o ícone de calendário ao lado abre qualquer data,
  inclusive as antigas.
- O rodapé do menu mostra a conexão: **Online · tempo real**, **Enviando alterações…** ou **Sem conexão**.
  Sem internet, dá para continuar marcando: as alterações são enviadas quando a conexão voltar.
- Se duas pessoas alterarem o mesmo registro ao mesmo tempo, cada campo fica com a última alteração recebida
  (uma pessoa mudando o status e outra a observação não se atrapalham).

## 4. Backup diário

- Uma tarefa agendada do Windows (**Backup Recebimento de Notas Fiscais**, todo dia às 18:00) roda `app/backup_diario.py` no computador
  e salva em `OneDrive - nagumo.com.br\Backup Recebimento de Notas Fiscais`:
  - `backup_recebimento_AAAA-MM-DD.json`: cópia completa (agendas, cargas antecipadas e lista de contratos), para restaurar;
  - `backup_recebimento_AAAA-MM-DD.xlsx`: a mesma cópia em planilha (abas Recebimento, Antecipadas e Fornecedores com Contrato);
  - `backup.log`: data e resultado de cada backup.
- Se o computador estiver desligado às 18:00, o backup roda assim que ele for ligado. São guardados os últimos 90 dias.
- **Restaurar**: menu **Importar relatório** > escolha o `.json` do backup. Volta o que estiver faltando, com as marcações; o que já
  existe não é repetido nem alterado.

## Bom saber

- **Segurança**: sem login, quem tem o link consegue ver e alterar os dados. Não divulgue o link fora da equipe.
  As regras só aceitam gravações no formato do app.
## Plano de consumo do banco (Firebase gratuito)

Limites do plano gratuito (Spark), por dia: 50 mil leituras, 20 mil gravações, 20 mil exclusões; espaço total de 1 GB.

| Item | Como está | Por quê |
|---|---|---|
| **Espaço** | ~0,4 MB em 26/11/2026 (1.446 agendas, contratos 59 KB, ruptura 166 KB); cresce ~6 KB por dia de agenda | Um documento por dia, campos com nomes curtos e sem campos vazios (~130 bytes por agenda); box, horário e paletes não são guardados. Nesse ritmo, 1 GB levaria décadas: não é preciso apagar dados antigos. |
| **Leituras ao abrir o app** | ~33 (dias dos últimos 7 dias + futuros, antecipações do mesmo período, contratos e ruptura) | O app acompanha em tempo real só os **últimos 7 dias e os futuros**. Data antiga aberta pelo calendário = 1 leitura; o dashboard carrega só o período escolhido. |
| **Leituras com o app aberto** | 1 por aparelho conectado a cada alteração no dia | Só o documento do dia alterado é reenviado; a mesma leitura serve para todos os registros daquele dia. |
| **Gravações** | 1 por marcação/edição | Grava só o campo alterado; importação grava 1 documento por dia do relatório; o fechamento automático dos dias anteriores grava 1 vez por dia. |
| **Listas de contratos e ruptura** | 1 documento cada, trocado inteiro na atualização diária | Uma leitura por aparelho a cada atualização. |
| **Backup diário** | lê todos os documentos 1 vez por dia (~60 leituras hoje) | Guarda tudo no OneDrive; restaura sem repetir. |

Estimativa com 15 pessoas abrindo o app 4 vezes por dia e ~400 marcações: cerca de 2 mil leituras ao abrir + 6 mil com o app
aberto = **~8 mil leituras por dia (16% do limite)** e ~500 gravações (2,5%).

Para acompanhar: Firebase Console > Firestore Database > **Uso**. Se um dia passar de 70% das leituras, diminua `JANELA_DIAS`
em `app/static/app.js` (hoje 7) ou peça para revisar.
