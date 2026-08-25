# NeoGuide
## Descrição do Projeto
O NeoGuide é uma solução tecnológica desenvolvida para automatizar e padronizar o cálculo de demanda elétrica em projetos de edificações com múltiplas unidades consumidoras, como condomínios residenciais, empreendimentos comerciais e instalações de uso misto.

## Tecnologias Usadas
As tecnologias usadas serão: Java, Spring Boot, React.

## Entregas
### Entrega 1

## Como Rodar o Projeto

## Time
- Guilherme Andradade - Full Stack
- Henrique Veloso - Back-end
- João Durão - Back-end
- Karollyne Barbosa - Front-end
- Lisa Sales - Scrum Master
- Maria Clara Bello - Product Owner
- Rhayssa Barbosa - Front-end
- Tarcísio Wanderley - Full Stack


## Histórias de Usuário

> **Contexto.** A Neoenergia Pernambuco reprova cerca de metade dos projetos de entrada de serviço na primeira análise, principalmente por aplicação de fatores da NBR 5410 no lugar das regras da norma da distribuidora (DIS-NOR-053). O Wizard guia o projetista no preenchimento, valida os dados, calcula a demanda da edificação conforme a norma vigente e gera o memorial técnico, que é enviado para análise da Neoenergia na própria plataforma.
>
> **Base documental.** A fórmula do memorial técnico (Anexo I da DIS-NOR-053) apresenta a demanda total como `D_ed = D_af + D_s + D_c + D_ve` (apartamentos residenciais pelo método da área útil, serviço do condomínio e cargas comerciais pelo método da carga instalada, e carregadores de veículos elétricos). Tabelas, fatores e limites numéricos da norma ainda não estão confirmados; tudo o que depender deles está marcado como **A validar com a Neoenergia/documentação** e consolidado na seção final.
>
> Autenticação de usuários (login) é pré-condição de todas as histórias e não é tratada como história própria neste documento.

| ID | História |
|---|---|
| US01 | Home com lista, ordenação e filtro de projetos |
| US02 | Criar novo projeto |
| US03 | Wizard — preenchimento guiado por etapas |
| US04 | Validação dos dados ao avançar de etapa |
| US05 | Cálculo automático da demanda |
| US06 | Geração do memorial técnico |
| US07 | Envio para análise, status e reenvio |
| US08 | Fila do analista da Neoenergia com filtros |
| US09 | Chatbot Lumi — dúvidas sobre a norma |
| US10 | Atendimento humano (escalonamento do chatbot) |
| US11 | Anexos do projeto |
| US12 | Importação do quadro de unidades por planilha |
| US13 | Duplicar projeto existente |
| US14 | Histórico de versões e trilha de auditoria |
| US15 | Norma versionada e rastreabilidade do cálculo |
| US16 | Simulador de expansão futura |
| US17 | Consulta de capacidade da rede na região |
| US18 | Compartilhamento do projeto |
| US19 | Painel de indicadores da Neoenergia |

---

# US01 — Home com lista, ordenação e filtro de projetos

## História de Usuário

Como um projetista, eu quero ver meus projetos com o status de cada um, podendo ordenar e filtrar a lista, para que eu localize rapidamente o projeto que preciso retomar ou consultar.

## Detalhes de negócio

* A Home lista apenas os projetos do usuário autenticado, exibindo nome, status e data da última alteração.
* Status possíveis: Rascunho, Calculado, Em análise, Aprovado, Reprovado.
* A lista pode ser ordenada por mais recentes (padrão), por data e por nome, e filtrada por status.
* Selecionar um projeto abre seus detalhes.

## Cenários de validação — BDD

### Cenário 1 — Positivo: ordenar projetos por nome

**Dado que** a projetista possua os projetos "Ed. Aurora", "Ed. Boa Viagem" e "Ed. Recife Sul" com status distintos
**Quando** escolher a ordenação por nome
**Então** a lista é exibida em ordem alfabética, mantendo o status visível em cada projeto.

### Cenário 2 — Negativo: filtro sem resultados

**Dado que** a projetista não possua nenhum projeto com status Reprovado
**Quando** aplicar o filtro "Reprovado"
**Então** o sistema exibe a mensagem de que nenhum projeto corresponde ao filtro
**E** oferece a opção de limpar o filtro.

---

# US02 — Criar novo projeto

## História de Usuário

Como um projetista, eu quero criar um projeto informando os dados de identificação da edificação, para que o cálculo de demanda comece organizado e vinculado à minha conta.

## Detalhes de negócio

* Campos obrigatórios: nome do projeto, endereço da edificação e tipo de edificação.
* **A validar com a Neoenergia:** tipos de edificação atendidos pelo sistema e quais etapas/categorias de carga correspondem a cada tipo.
* O tipo de edificação selecionado define quais etapas o Wizard apresentará (US03).
* O projeto é criado com status Rascunho.

## Cenários de validação — BDD

### Cenário 1 — Positivo: criar projeto com dados válidos

**Dado que** a projetista esteja na tela "Novo projeto"
**Quando** preencher nome, endereço e tipo de edificação e confirmar
**Então** o projeto é criado com status Rascunho, vinculado à sua conta
**E** o Wizard abre na primeira etapa correspondente ao tipo selecionado.

### Cenário 2 — Negativo: campo obrigatório ausente

**Dado que** a projetista esteja na tela "Novo projeto"
**Quando** tentar confirmar sem selecionar o tipo de edificação
**Então** o sistema não cria o projeto
**E** destaca o campo pendente com a orientação de preenchimento.

---

# US03 — Wizard: preenchimento guiado por etapas

## História de Usuário

Como um projetista, eu quero informar os dados da edificação em etapas sequenciais, organizadas por categoria de carga, para que eu preencha apenas o que é relevante ao meu projeto, sem me perder no processo.

## Detalhes de negócio

* O Wizard apresenta somente as etapas relevantes ao tipo de edificação selecionado na criação do projeto, encerrando com uma etapa de revisão.
* **A validar com a Neoenergia:** categorias de carga e dados exigidos em cada etapa. A referência inicial é o memorial do Anexo I (apartamentos por área útil, serviço do condomínio, cargas comerciais e veículos elétricos).
* Um indicador de progresso mostra a etapa atual; o projetista pode voltar a etapas já preenchidas.
* Os dados são salvos automaticamente a cada avanço, permitindo retomar o rascunho depois.

## Cenários de validação — BDD

### Cenário 1 — Positivo: avançar mantendo os dados

**Dado que** a projetista tenha preenchido a etapa de apartamentos com 30 unidades
**Quando** avançar para a etapa seguinte e depois retornar
**Então** a etapa de apartamentos exibe as 30 unidades informadas.

### Cenário 2 — Negativo: etapa não pertencente ao tipo de edificação

**Dado que** o projeto seja de um tipo que não possui unidades residenciais
**Quando** a projetista percorrer o Wizard
**Então** a etapa de apartamentos não é exibida nem aparece no indicador de progresso.

### Cenário 3 — Positivo: retomar rascunho

**Dado que** a projetista tenha saído da plataforma no meio do preenchimento
**Quando** reabrir o projeto pela Home
**Então** o Wizard abre na etapa em que ela parou, com as etapas anteriores preservadas.

---

# US04 — Validação dos dados ao avançar de etapa

## História de Usuário

Como um projetista, eu quero que o sistema valide os dados quando eu clicar em "Avançar", para que erros sejam corrigidos na hora e não cheguem ao cálculo nem à análise da Neoenergia.

## Detalhes de negócio

* A validação ocorre ao clicar em "Avançar". Havendo dados inválidos ou obrigatórios ausentes, o sistema não permite avançar.
* Cada campo com problema é indicado no formulário, com o motivo do erro e o que precisa ser corrigido.
* Validações básicas: campos obrigatórios preenchidos, valores numéricos onde exigido, valores maiores que zero onde não fizer sentido valor nulo.
* **A validar com a Neoenergia/documentação:** regras normativas de validação (fatores de simultaneidade, tratamento de carregadores de veículos elétricos, fator de potência mínimo, ar-condicionado em quantidade, diversidade entre blocos).

## Cenários de validação — BDD

### Cenário 1 — Positivo: avançar com dados válidos

**Dado que** a projetista tenha preenchido todos os campos obrigatórios da etapa com valores válidos
**Quando** clicar em "Avançar"
**Então** o sistema exibe a próxima etapa do Wizard.

### Cenário 2 — Negativo: avançar com dado inválido

**Dado que** a projetista tenha informado um apartamento com área útil de 0 m²
**Quando** clicar em "Avançar"
**Então** o sistema permanece na etapa atual
**E** destaca o campo com a mensagem "Informe uma área útil maior que zero".

---

# US05 — Cálculo automático da demanda

## História de Usuário

Como um projetista, eu quero que o sistema calcule automaticamente a demanda da edificação a partir dos dados preenchidos, para que eu obtenha o valor correto pela norma da Neoenergia sem cálculo manual.

## Detalhes de negócio

* O cálculo usa os dados do Wizard e as regras da norma vigente, seguindo a estrutura do memorial do Anexo I: `D_ed = D_af + D_s + D_c + D_ve`.
* **A validar com a Neoenergia/documentação:** tabelas, fatores, conversões e limites aplicados em cada parcela, além de faixas de atendimento ou sugestão de transformador.
* O resultado é apresentado de forma compreensível, discriminado por parcela e total, indicando as regras aplicadas.
* O cálculo só é executado sem pendências de validação; alterações nos dados exigem recálculo. Com o cálculo concluído, o projeto passa ao status Calculado.

## Cenários de validação — BDD

### Cenário 1 — Positivo: calcular com dados completos

**Dado que** todas as etapas do Wizard estejam preenchidas e válidas
**Quando** a projetista concluir a etapa de revisão
**Então** o sistema calcula e exibe a demanda por parcela e o total
**E** o projeto passa ao status Calculado.

### Cenário 2 — Negativo: calcular com pendência

**Dado que** exista um campo obrigatório sem preenchimento em uma etapa
**Quando** a projetista tentar concluir a revisão
**Então** o sistema não executa o cálculo
**E** lista a etapa e o campo pendentes.

### Cenário 3 — Positivo: recalcular após alteração

**Dado que** o projeto esteja Calculado
**Quando** a projetista alterar um dado que influencia o cálculo
**Então** o resultado anterior é invalidado e o sistema recalcula, exibindo o valor atualizado.

---

# US06 — Geração do memorial técnico

## História de Usuário

Como um projetista, eu quero que o memorial técnico seja gerado automaticamente após o cálculo, para que eu tenha o documento no padrão da Neoenergia sem montá-lo à mão.

## Detalhes de negócio

* O memorial é gerado automaticamente, em PDF, assim que o cálculo é concluído com sucesso (US05); não há ação manual de geração.
* O documento segue o modelo do Anexo I da DIS-NOR-053, com os dados da edificação, as parcelas de demanda, o total e as regras aplicadas.
* Recalcular o projeto gera um novo memorial, substituindo o atual; versões anteriores permanecem no histórico (US14).
* O memorial fica disponível para download na tela do projeto.

## Cenários de validação — BDD

### Cenário 1 — Positivo: memorial gerado após o cálculo

**Dado que** a projetista tenha concluído o cálculo do projeto
**Quando** o sistema finalizar o processamento
**Então** o memorial em PDF fica disponível para download na tela do projeto, refletindo o resultado calculado.

### Cenário 2 — Negativo: projeto sem cálculo não possui memorial

**Dado que** um projeto esteja em Rascunho, sem cálculo concluído
**Quando** a projetista acessar a área de documentos do projeto
**Então** nenhum memorial é exibido
**E** o sistema informa que o memorial será gerado automaticamente após a conclusão do cálculo.

---

# US07 — Envio para análise, status e reenvio

## História de Usuário

Como um projetista, eu quero enviar o projeto para análise da Neoenergia, acompanhar o status e reenviar após correções, para que todo o processo de aprovação aconteça dentro da plataforma.

## Detalhes de negócio

* Pré-requisitos do envio: projeto Calculado (o memorial já existe, pois é gerado automaticamente — US06) e anexos obrigatórios presentes (US11).
* Após confirmação do envio, o projeto recebe um número de protocolo, passa ao status Em análise e fica somente leitura; o projetista recebe um e-mail de confirmação contendo o protocolo.
* Concluída a análise (US08), o status muda para Aprovado ou Reprovado; a justificativa de reprovação fica visível ao projetista.
* Um projeto Reprovado pode ser reaberto como nova versão para correção e reenvio, preservando o histórico.

## Cenários de validação — BDD

### Cenário 1 — Positivo: enviar projeto completo

**Dado que** o projeto esteja Calculado e com os anexos obrigatórios presentes
**Quando** a projetista confirmar o envio
**Então** o projeto recebe um protocolo e passa ao status Em análise
**E** a projetista recebe um e-mail de confirmação com o número do protocolo.

### Cenário 2 — Negativo: enviar com pendência obrigatória

**Dado que** o projeto esteja Calculado, mas sem um anexo obrigatório
**Quando** a projetista tentar enviar
**Então** o sistema não realiza o envio
**E** exibe a lista de pendências que impedem a submissão, indicando o anexo faltante.

### Cenário 3 — Positivo: reenviar após reprovação

**Dado que** o projeto tenha sido Reprovado com justificativa registrada
**Quando** a projetista reabrir o projeto para correção
**Então** o sistema cria uma nova versão editável, mantendo a versão anterior e a justificativa no histórico.

---

# US08 — Fila do analista da Neoenergia com filtros

## História de Usuário

Como um analista da Neoenergia, eu quero consultar a fila de projetos enviados com filtros personalizados e registrar o resultado da análise, para que eu priorize meu trabalho e conclua as análises com menos esforço.

## Detalhes de negócio

* A fila lista os projetos Em análise, com filtros por município, tipo de edificação, faixa de demanda e projetista, ordenados por data de envio.
* Ao abrir um projeto, o analista visualiza os dados preenchidos, o resultado do cálculo, o memorial e os anexos, em modo somente leitura.
* O analista registra Aprovado ou Reprovado; a reprovação exige justificativa.
* O registro atualiza o status do projeto e notifica o projetista.

## Cenários de validação — BDD

### Cenário 1 — Positivo: filtrar a fila e aprovar um projeto

**Dado que** existam projetos Em análise de vários municípios
**Quando** o analista filtrar por "Recife", abrir um projeto e registrar a aprovação
**Então** a fila exibe apenas os projetos de Recife antes da abertura
**E** o projeto aprovado muda de status e sai da fila.

### Cenário 2 — Negativo: reprovar sem justificativa

**Dado que** o analista esteja registrando o resultado de um projeto
**Quando** selecionar "Reprovar" e tentar confirmar sem informar a justificativa
**Então** o sistema não registra o resultado
**E** informa que a justificativa é obrigatória.

---

# US09 — Chatbot Lumi: dúvidas sobre a norma

Opções de nome consideradas: **Lumi** (de *lumen*, luz), **Luzia** (nome tradicional do Nordeste, região da concessão) e **Clara** (claridade/iluminação). Escolhemos **Lumi** por ser curto, fácil de lembrar e remeter diretamente à luz, combinando com a identidade visual de uma bolha flutuante sempre presente na tela.

## História de Usuário

Como um projetista, eu quero tirar dúvidas com a assistente virtual Lumi durante o preenchimento, para que eu esclareça pontos da norma sem sair do Wizard nem interpretar o documento sozinho.

## Detalhes de negócio

* A Lumi aparece como uma bolha flutuante, sempre visível durante a navegação no Wizard; ao clicar, abre a conversa.
* As respostas são geradas por RAG sobre a base documental disponibilizada (DIS-NOR-053 e materiais de apoio), citando a seção consultada.
* Quando não encontrar informação confiável na base, a Lumi informa que não sabe responder — sem inventar — e oferece o encaminhamento para o suporte humano (US10).
* A Lumi apenas orienta; não altera dados do projeto.

## Cenários de validação — BDD

### Cenário 1 — Positivo: responder dúvida coberta pela base

**Dado que** a projetista esteja em uma etapa do Wizard
**Quando** perguntar à Lumi como a norma trata um item presente na base documental
**Então** a Lumi responde com base nos documentos
**E** cita a seção da norma consultada.

### Cenário 2 — Negativo: pergunta fora da base

**Dado que** a projetista faça uma pergunta cuja resposta não esteja na base documental
**Quando** a Lumi processar a pergunta
**Então** ela informa que não possui informação confiável para responder
**E** oferece o encaminhamento da dúvida para o suporte humano.

---

# US10 — Atendimento humano (escalonamento do chatbot)

## História de Usuário

Como um projetista, eu quero encaminhar minha dúvida para um atendente da Neoenergia quando a Lumi não conseguir respondê-la, para que casos não cobertos pela norma não travem meu projeto.

## Detalhes de negócio

* O encaminhamento é oferecido pela Lumi quando ela não resolve a dúvida (US09); o projetista também pode solicitá-lo diretamente na conversa.
* O chamado leva automaticamente o contexto: projeto, etapa atual e transcrição da conversa — o projetista não reexplica o caso.
* O atendimento é assíncrono: o projetista continua trabalhando e é notificado quando houver resposta; a conversa fica registrada no projeto.
* O atendente orienta, mas não edita os dados do projeto.

## Cenários de validação — BDD

### Cenário 1 — Positivo: escalonar e receber resposta

**Dado que** a Lumi não tenha conseguido responder a dúvida da projetista
**Quando** a projetista confirmar o encaminhamento ao suporte humano
**Então** um chamado é aberto com o contexto do projeto e da conversa anexado
**E** a projetista é notificada quando o atendente responder.

### Cenário 2 — Negativo: atendente não altera o projeto

**Dado que** um atendente esteja tratando o chamado de um projeto
**Quando** visualizar os dados anexados ao chamado
**Então** os dados são exibidos em modo somente leitura
**E** nenhuma ação de edição do projeto está disponível para o atendente.

---

# US11 — Anexos do projeto

## História de Usuário

Como um projetista, eu quero anexar ao projeto os documentos exigidos e ver o que ainda falta, para que meu projeto não seja devolvido por documentação incompleta.

## Detalhes de negócio

* O painel de documentos apresenta um checklist com os anexos obrigatórios e opcionais do projeto, indicando o que já foi enviado.
* **A validar com a Neoenergia/documentação:** quais documentos são obrigatórios e se a exigência varia conforme a característica do projeto.
* Formatos aceitos: PDF, PNG, JPG e DWG.
* Anexos obrigatórios pendentes bloqueiam o envio para análise (US07).

## Cenários de validação — BDD

### Cenário 1 — Positivo: anexar documento do checklist

**Dado que** o checklist do projeto exiba um documento obrigatório pendente
**Quando** a projetista anexar o arquivo em formato aceito
**Então** o item passa a constar como enviado no checklist.

### Cenário 2 — Negativo: formato não suportado

**Dado que** a projetista tente anexar um arquivo em formato não aceito
**Quando** confirmar o upload
**Então** o sistema recusa o arquivo
**E** informa os formatos aceitos.

---

# US12 — Importação do quadro de unidades por planilha

## História de Usuário

Como um projetista de empreendimento com muitas unidades, eu quero importar o quadro de apartamentos a partir de uma planilha, para que eu não precise digitar unidade por unidade nem errar na transcrição.

## Detalhes de negócio

* A plataforma disponibiliza um modelo de planilha com as colunas esperadas.
* Antes de aplicar, o sistema exibe uma pré-visualização com as linhas válidas e as linhas com problema, indicando linha e coluna de cada erro; apenas as válidas são importadas.
* O projetista escolhe entre substituir os dados atuais ou somar aos existentes; a substituição pede confirmação.
* Os dados importados ficam editáveis no Wizard e passam pelas validações da US04.

## Cenários de validação — BDD

### Cenário 1 — Positivo: importar planilha válida

**Dado que** a projetista importe uma planilha no modelo com 180 unidades válidas
**Quando** confirmar a importação após a pré-visualização
**Então** as 180 unidades aparecem na etapa de apartamentos do Wizard.

### Cenário 2 — Negativo: planilha com linhas inválidas

**Dado que** a planilha importada contenha 4 linhas com área útil vazia
**Quando** o sistema processar o arquivo
**Então** a pré-visualização aponta as 4 linhas com o erro e sua localização
**E** somente as linhas válidas são importadas após a confirmação.

---

# US13 — Duplicar projeto existente

## História de Usuário

Como um projetista que atende empreendimentos padronizados, eu quero criar um projeto a partir de um anterior, para que eu aproveite a estrutura de cargas já montada e altere apenas o que muda.

## Detalhes de negócio

* A cópia inclui os dados da edificação e das etapas do Wizard; não inclui memorial, anexos, protocolo, histórico de análise nem status.
* O novo projeto nasce em Rascunho, com nome sugerido editável, e exige a confirmação do endereço.
* O cálculo não é herdado: a cópia passa novamente pela revisão e pelo cálculo, aplicando a norma vigente (US15).

## Cenários de validação — BDD

### Cenário 1 — Positivo: duplicar projeto aprovado

**Dado que** a projetista possua um projeto Aprovado
**Quando** selecionar "Duplicar", confirmar o novo nome e o novo endereço
**Então** um projeto em Rascunho é criado com as mesmas cargas
**E** sem memorial, anexos ou protocolo.

### Cenário 2 — Negativo: duplicar mantendo o mesmo endereço

**Dado que** a projetista esteja duplicando um projeto
**Quando** tentar confirmar sem alterar o endereço original
**Então** o sistema alerta que já existe projeto naquele endereço
**E** exige confirmação explícita antes de prosseguir.

---

# US14 — Histórico de versões e trilha de auditoria

## História de Usuário

Como um projetista, eu quero consultar o que mudou no projeto entre as versões enviadas, para que eu entenda a evolução entre reenvios e tenha respaldo sobre o que foi analisado.

## Detalhes de negócio

* Cada envio fecha uma versão numerada com os dados, o cálculo, o memorial e os anexos daquele momento; versões são somente leitura e não são apagadas.
* O histórico registra os eventos relevantes do projeto com data, hora e autor.
* O projetista pode comparar duas versões e ver as diferenças de dados e de demanda.
* O analista visualiza apenas o histórico a partir do primeiro envio, não os eventos de rascunho.

## Cenários de validação — BDD

### Cenário 1 — Positivo: comparar versões após reenvio

**Dado que** um projeto tenha sido reprovado na v1 e reenviado como v2
**Quando** a projetista comparar as duas versões
**Então** o sistema destaca as diferenças de dados de entrada
**E** a variação da demanda entre as versões.

### Cenário 2 — Negativo: analista não acessa eventos de rascunho

**Dado que** um projeto possua eventos anteriores ao primeiro envio
**Quando** o analista consultar o histórico
**Então** apenas os eventos a partir do envio da v1 são exibidos.

---

# US15 — Norma versionada e rastreabilidade do cálculo

## História de Usuário

Como um analista da Neoenergia, eu quero que as regras da norma sejam cadastradas de forma versionada e que cada cálculo registre a versão aplicada, para que atualizações normativas não invalidem silenciosamente projetos já submetidos.

## Detalhes de negócio

* Os parâmetros de cálculo ficam cadastrados como conjunto de regras versionado, com vigência definida; apenas um conjunto fica vigente por vez.
* Todo cálculo usa o conjunto vigente e registra no resultado e no memorial qual versão foi aplicada.
* Projetos já enviados preservam a versão sob a qual foram submetidos; rascunhos cuja norma mudou recebem aviso ao serem reabertos.
* O cadastro de novas versões é restrito ao perfil administrador da Neoenergia.

## Cenários de validação — BDD

### Cenário 1 — Positivo: cálculo registra a versão aplicada

**Dado que** exista um conjunto de regras vigente
**Quando** a projetista concluir o cálculo
**Então** o resultado e o memorial indicam a versão da norma utilizada.

### Cenário 2 — Negativo: projeto enviado não é recalculado com a norma nova

**Dado que** um projeto tenha sido enviado sob uma versão da norma
**Quando** uma nova versão entrar em vigência
**Então** o projeto enviado mantém o cálculo e a versão originais, sem recálculo automático.

---

# US16 — Simulador de expansão futura

## História de Usuário

Como um projetista, eu quero simular acréscimos de carga sobre um projeto calculado, para que o cliente decida se dimensiona a entrada com folga agora em vez de refazer a obra no futuro.

## Detalhes de negócio

* A simulação parte de um projeto Calculado e cria um cenário paralelo, sem alterar o projeto original.
* Cenários previstos: acréscimo de carregadores de veículos elétricos e de cargas de condomínio. **A validar com a Neoenergia/documentação:** tratamento de geração fotovoltaica na demanda.
* O resultado mostra a demanda atual, a simulada e a diferença.
* Um cenário pode ser promovido a novo projeto (via duplicação — US13).

## Cenários de validação — BDD

### Cenário 1 — Positivo: simular acréscimo de carregadores

**Dado que** um projeto esteja Calculado
**Quando** a projetista simular o acréscimo de carregadores de veículos elétricos
**Então** o sistema exibe a demanda atual, a simulada e a diferença
**E** o projeto original permanece inalterado.

### Cenário 2 — Negativo: simular sobre projeto sem cálculo

**Dado que** um projeto esteja em Rascunho, sem cálculo concluído
**Quando** a projetista tentar abrir o simulador
**Então** o sistema informa que a simulação exige um projeto calculado
**E** não cria o cenário.

---

# US17 — Consulta de capacidade da rede na região

## História de Usuário

Como um projetista, eu quero uma indicação prévia de que a rede da região comporta a demanda calculada, para que eu antecipe a possibilidade de obra de reforço antes de submeter o projeto.

## Detalhes de negócio

* A consulta cruza o endereço do projeto e a demanda calculada com os dados de capacidade disponibilizados pela Neoenergia. **A validar com a Neoenergia:** disponibilidade e granularidade desses dados.
* O resultado é indicativo e não vinculante; a tela deixa explícito que a confirmação depende da análise formal.
* Sem dados para a região, o sistema informa a indisponibilidade em vez de estimar.
* O resultado e a data da consulta ficam registrados no histórico do projeto.

## Cenários de validação — BDD

### Cenário 1 — Positivo: consulta com dados disponíveis

**Dado que** um projeto Calculado esteja em região com dados de capacidade disponíveis
**Quando** a projetista solicitar a consulta
**Então** o sistema exibe a indicação de atendimento para a demanda calculada
**E** o aviso de que a indicação não substitui a análise formal.

### Cenário 2 — Negativo: região sem dados

**Dado que** não existam dados de capacidade para a região do projeto
**Quando** a projetista solicitar a consulta
**Então** o sistema informa que a informação não está disponível
**E** não apresenta estimativa.

---

# US18 — Compartilhamento do projeto

## História de Usuário

Como um projetista, eu quero compartilhar um projeto com o cliente ou com um colega, definindo o nível de acesso, para que a revisão e a prestação de contas aconteçam sem exportar dados por e-mail.

## Detalhes de negócio

* Níveis de acesso: Leitor (visualiza dados, resultado, memorial e status) e Colaborador (edita dados e recalcula).
* O envio para análise e a exclusão do projeto permanecem exclusivos do proprietário (responsável técnico).
* O proprietário pode revogar o acesso a qualquer momento, com efeito imediato.
* Edições de colaboradores ficam identificadas no histórico (US14).

## Cenários de validação — BDD

### Cenário 1 — Positivo: compartilhar como leitor

**Dado que** a proprietária compartilhe o projeto com o cliente no nível Leitor
**Quando** o cliente acessar o projeto
**Então** ele visualiza os dados, o resultado e o memorial
**E** não vê ações de edição, cálculo ou envio.

### Cenário 2 — Negativo: colaborador tenta enviar para análise

**Dado que** um colega possua acesso Colaborador ao projeto
**Quando** procurar a ação de envio para análise
**Então** a ação não está disponível para o seu perfil de acesso.

---

# US19 — Painel de indicadores da Neoenergia

## História de Usuário

Como um gestor da Neoenergia, eu quero acompanhar indicadores dos projetos recebidos e dos motivos de reprovação, para que eu identifique onde o processo precisa melhorar e meça a redução do retrabalho.

## Detalhes de negócio

* Indicadores mínimos, com filtro por período e município: projetos recebidos/aprovados/reprovados, taxa de reprovação na primeira análise, ranking de motivos de reprovação e tempo médio de análise.
* Os dados são apresentados de forma agregada.
* O acesso é restrito aos perfis da Neoenergia.

## Cenários de validação — BDD

### Cenário 1 — Positivo: consultar indicadores do período

**Dado que** existam projetos analisados no trimestre selecionado
**Quando** o gestor filtrar o painel pelo período
**Então** o sistema exibe os totais e a taxa de reprovação correspondentes ao filtro.

### Cenário 2 — Negativo: acesso por perfil não autorizado

**Dado que** uma usuária possua o perfil Projetista
**Quando** tentar acessar o painel de indicadores
**Então** o sistema nega o acesso
**E** informa que a área é restrita à Neoenergia.

---

## Matriz de esforço e impacto

Classificação das histórias por **impacto** (quanto a funcionalidade contribui para reduzir as reprovações e entregar valor ao projetista/Neoenergia) e **esforço** (complexidade de construção, incluindo dependência de regras da norma ainda não confirmadas).

### Fazer agora — alto impacto, pouco esforço

| História | Por quê |
|---|---|
| US01 — Home com lista, ordenação e filtro | Porta de entrada do sistema; CRUD simples com alto ganho de usabilidade. |
| US02 — Criar novo projeto | Pré-requisito de todo o fluxo e de baixa complexidade. |
| US11 — Anexos do projeto | Documentação incompleta é motivo recorrente de devolução, e o checklist é barato de construir. |
| US13 — Duplicar projeto existente | Reaproveita o que já existe, quase sem código novo, e economiza muito tempo em empreendimentos padronizados. |

### Programar para fazer — alto impacto, alto esforço

| História | Por quê |
|---|---|
| US03 — Wizard por etapas | Núcleo do produto; muitas telas e regras de navegação. |
| US04 — Validação ao avançar | Onde o problema das reprovações é atacado; depende das regras da norma. |
| US05 — Cálculo automático da demanda | Coração da solução; depende das tabelas e fatores a confirmar. |
| US06 — Geração do memorial técnico | Entregável final exigido pela Neoenergia; geração de PDF em modelo específico. |
| US07 — Envio, status e reenvio | Fecha o ciclo com a distribuidora; envolve protocolo, e-mail e versionamento. |
| US08 — Fila do analista com filtros | Sem ela o processo não se completa do lado da Neoenergia. |
| US09 — Chatbot Lumi | Alto valor percebido, mas exige base documental, RAG e curadoria. |
| US10 — Atendimento humano | Completa a experiência de suporte; exige fila, notificações e definição de SLA. |
| US15 — Norma versionada e rastreabilidade | Habilitador da US05: deve ser construída **junto** com o cálculo, pois refatorar depois é caro. |

### Fazer quando der — baixo impacto, pouco esforço

| História | Por quê |
|---|---|
| US12 — Importação por planilha | Beneficia apenas empreendimentos grandes; leitura de arquivo é relativamente simples. |
| US16 — Simulador de expansão futura | Reaproveita o motor de cálculo já pronto; valor é comercial, não normativo. |
| US18 — Compartilhamento do projeto | Conveniência de colaboração, contornável por outros meios no início. |

### Não fazer agora — baixo impacto (no MVP), muito esforço

| História | Por quê |
|---|---|
| US14 — Histórico e trilha de auditoria completa | O versionamento mínimo já vem embutido na US07; a auditoria completa é cara e pouco usada no início. |
| US17 — Consulta de capacidade da rede | Depende de dados que a Neoenergia pode não disponibilizar e carrega risco de o projetista tratar a indicação como aprovação. |
| US19 — Painel de indicadores | Só faz sentido com volume de dados acumulado; o gestor não é usuário do MVP. |

> As histórias em "Não fazer agora" permanecem no backlog: a classificação é sobre a primeira versão do produto, não descarte definitivo.

## Pontos que ainda precisam ser confirmados com a Neoenergia

1. **Tipos de edificação** atendidos pelo sistema e quais etapas/categorias de carga correspondem a cada tipo (US02, US03).
2. **Tabelas e fatores da DIS-NOR-053**: demanda por área útil, fatores de simultaneidade, fatores de demanda por grupo de carga, tratamento de carregadores de veículos elétricos, fator de potência mínimo, ar-condicionado em quantidade e diversidade entre blocos (US04, US05).
3. **Faixas de atendimento** e eventual sugestão de transformador a partir da demanda calculada (US05).
4. **Documentos obrigatórios** do projeto e se a exigência varia por característica da edificação (US11).
5. **Tratamento de geração fotovoltaica** no cálculo/simulação de demanda (US16).
6. **Dados de capacidade da rede**: existência, granularidade e forma de acesso (US17).
7. **Atendimento humano**: horário de funcionamento e prazo de resposta a serem comunicados ao projetista (US10).

## Inconsistências ou dúvidas encontradas

1. **Momento de geração do memorial (resolvido nesta revisão):** as versões anteriores tinham geração manual (botão) na antiga US07 e pré-requisito de "memorial gerado" na antiga US08. Adotamos um único fluxo: o memorial é **gerado automaticamente após o cálculo** (US06); o envio (US07) apenas pressupõe projeto Calculado e anexos completos. Se a equipe preferir a geração manual, US06 e US07 precisam ser ajustadas juntas.
2. **Autenticação sem história própria:** com a exclusão da antiga US01, o login passa a ser pré-condição implícita. A equipe deve decidir onde essa funcionalidade será registrada (backlog técnico, história futura ou escopo de infraestrutura), pois telas de login provavelmente ainda constarão no protótipo.
3. **Validação bloqueante vs. correção automática:** a antiga US05 aplicava correções normativas automáticas com aviso (ex.: ajuste de fator). Nesta revisão, toda divergência bloqueia o avanço (US04). Quando as regras da norma forem confirmadas, a equipe deve decidir se algum ajuste automático volta ao escopo — isso impactaria também o conteúdo do memorial.
4. **Perfil "gestor" (US19):** o painel pressupõe um perfil de gestão da Neoenergia distinto do analista. Confirmar se esse papel existirá ou se o acesso será do próprio analista/administrador.
5. **Notação da fórmula:** o Anexo I usa `D_af` para a parcela dos apartamentos; versões anteriores do documento usavam `D_ap`. Padronizamos como `D_af`, seguindo o documento.


Matriz de Prioridades das Histórias de Usuários
<img width="1600" height="1134" alt="WhatsApp Image 2026-08-25 at 19 41 35" src="https://github.com/user-attachments/assets/e30e2db5-4259-4b55-9b07-0ed475254d20" />


