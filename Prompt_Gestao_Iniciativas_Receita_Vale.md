# PROMPT MASTER — GESTÃO DE INICIATIVAS DE RECEITA

## 1. Missão

Atue como arquiteto de software sênior, desenvolvedor e especialista em UX corporativa, gestão de portfólios, venda de ativos e valorização de resíduos.

Desenvolva o aplicativo “Gestão de Iniciativas de Receita” para acompanhar oportunidades de venda de ativos, sucatas, materiais inservíveis e outras categorias editáveis. Cada iniciativa é a unidade principal de gestão e pode ter várias ações, vendas e recebimentos.

O sistema deve responder: qual o potencial da carteira, o que impede a disponibilização, quem deve agir, quando ocorrerão disponibilização, venda e recebimento, quanto foi vendido e quanto foi recebido.

## 2. Escopo e arquitetura

Entregue a primeira versão funcional em um único index.html, com HTML5, CSS3 e JavaScript incorporados, sem compilação e preferencialmente sem CDN. Utilize componentes e módulos internos organizados e camada de repositório desacoplada da interface.

Esta entrega é um protótipo local com IndexedDB, backup JSON e uso por uma pessoa em cada base. Dados pertencem ao navegador e à origem de acesso; copiar o HTML ou enviá-lo a outro computador não transfere a base. Importar backup não equivale a sincronização.

O requisito definitivo é uso compartilhado por equipe em computadores diferentes. Documente a evolução necessária: API, banco central, autenticação, autorização por perfil, concorrência, backup central e auditoria. Não apresente esses recursos como implementados na versão local. Não armazene segredos no HTML nem crie login local com aparência de segurança corporativa.

Informe como servir o HTML em uma origem local estável e as limitações de abertura por file://. Não prometa funcionamento offline ou em navegadores sem validar. Alterações de porta/origem podem tornar outra base visível.

Interface em português brasileiro, moeda BRL, datas exibidas em dd/mm/aaaa e layout para desktop, notebook e tablet. Não implementar integrações externas sem especificação.

## 3. Identidade visual

Use paleta inspirada na Vale: verde #007E7A como principal, aqua #0ABB98 como apoio, azul #3CB5E5 e amarelo #ECB11F para destaques. Use neutros para superfícies e textos; laranja e vermelho apenas para alertas e estados funcionais. Verifique contraste; não use somente cor para comunicar status. Não inserir logotipo sem arquivo autorizado.

Estética executiva e sóbria, menu lateral recolhível, cabeçalho com ações rápidas, KPIs legíveis, filtros consistentes, tabelas com ordenação e paginação, formulários agrupados, abas e painéis de detalhe. Priorize densidade útil, acessibilidade por teclado, rótulos e feedback de salvamento.

## 4. Modelo de dados

Use IDs estáveis e independentes dos nomes, relacionamentos explícitos e datas de criação/alteração. Separe entidades:

- Iniciativa.
- Ação e dependências.
- Revisão de estimativa.
- Venda e parcelas previstas.
- Recebimento vinculado à venda.
- Histórico de eventos.
- Referências documentais.
- Matchcodes e modelos de fluxo.

Não agregue valores financeiros após juntar relações de um-para-muitos sem antes consolidar cada entidade: uma venda com três ações não pode ser somada três vezes.

### Iniciativa

Identificação: ID, título, descrição, tipo de captura, categoria, subcategoria, unidade operacional, localidade/origem, área demandante, responsável principal, data de identificação, prioridade e observações.

Gestão: modelo e versão do fluxo, etapa, situação, bloqueios com motivo e data, datas previstas e reais de disponibilização e comercialização, previsão de recebimento, motivo/data de encerramento e arquivamento.

Financeiro: estimativa original preservada, revisões com justificativa, quantidade e unidade opcionais, preço unitário opcional, valores vendidos e recebidos calculados dos eventos. Registrar se a estimativa foi informada diretamente ou calculada por quantidade × preço. Não sobrescrever valores sem confirmação.

Obrigatórios no cadastro: título, tipo, unidade, responsável, data de identificação e etapa inicial. Permita estimativa ainda não informada, distinguindo ausência de informação de valor zero.

Permita criar, editar, pesquisar, filtrar, ordenar, duplicar e arquivar. Duplicação gera novo ID e copia apenas cadastro e, opcionalmente, modelo de ações; não copia vendas, recebimentos, conclusões ou histórico.

### Ações

Campos: ID, iniciativa, descrição, responsável, criação, início previsto, prazo original, prazo vigente, conclusão efetiva, status, prioridade, dependências, impedimentos, comentários e referências documentais.

Guarde histórico de mudanças de prazo, responsável e status. Reprogramação exige justificativa e preserva prazo original. Conclusão exige data efetiva; reabertura é registrada sem apagar a conclusão anterior do histórico.

Impeça dependência da própria ação, ciclos e referências inexistentes. Inicialmente limite dependências a ações da mesma iniciativa. Bloqueie conclusão com predecessoras pendentes, permitindo exceção explícita e justificada no histórico.

Progresso operacional = ações concluídas ÷ ações não canceladas; sem ações elegíveis, exiba “Sem plano”, não 100%. Progresso operacional não representa captura financeira.

### Vendas, parcelas e recebimentos

Venda: ID, iniciativa, descrição/lote, data de formalização, valor bruto em BRL, referência documental, observações e estado válido/cancelado. Uma iniciativa pode ter vendas parciais e continuar em comercialização.

Parcela: venda, vencimento previsto e valor previsto. Permitir várias parcelas por venda; soma deve conciliar com o valor da venda ou mostrar pendência explícita. Previsão de caixa de vendas formalizadas vem dessas parcelas.

Recebimento: ID, venda obrigatoriamente vinculada, data efetiva, valor, referência financeira e observações. A iniciativa é derivada da venda. Permitir vários recebimentos por venda e, se necessário, alocação às parcelas. Adiantamentos sem venda ficam fora do escopo inicial.

Use centavos inteiros ou aritmética decimal confiável. Não somar números monetários com erros de ponto flutuante. Valores de venda/recebimento devem ser positivos; correções usam cancelamento/estorno rastreável, preservando original. Impedir estorno acima do valor líquido original e cancelamento de venda com recebimentos líquidos sem resolução prévia.

Bloqueie recebimento acima do saldo da venda e informe como corrigir o cadastro. Alertar potencial duplicidade por documento, data e valor. Recebimento futuro não pode ser lançado como efetivo; registrar como previsão.

Na primeira versão, “Receita realizada — critério caixa” significa recebimentos líquidos efetivos. Explique que não equivale a faturamento ou reconhecimento contábil. Use bases brutas comparáveis; tributos, comissões e custos ficam fora do cálculo inicial e essa convenção deve aparecer nas definições.

## 5. Regras financeiras e indicadores

Estimativa original: primeiro potencial registrado; revisões não a alteram. Se ausente no cadastro, a primeira estimativa torna-se a original com data de registro.

Estimativa vigente: última revisão válida ou original quando não há revisão. Toda revisão exige data, valor e justificativa e preserva a anterior. A estimativa representa o valor total esperado da iniciativa, incluindo parcelas já vendidas; não é apenas o saldo a vender.

Valor vendido: soma das vendas válidas líquidas de cancelamentos. Recebido: soma dos recebimentos efetivos líquidos de estornos. Saldo a receber: vendido menos recebido. Potencial ainda não vendido: máximo entre estimativa vigente menos vendido e zero, identificado como estimativa gerencial. Mostre alerta quando vendido supera a estimativa; não altere a estimativa automaticamente.

Não some estimativa, vendido e recebido como receitas adicionais: são visões do mesmo ciclo. Não use o potencial ainda não vendido como previsão de caixa firme.

Separe previsão comercial de oportunidades e previsão de caixa das parcelas vendidas. Na primeira versão, datas e valores previstos de oportunidades são indicativos e apresentados separadamente das parcelas, evitando contagem duplicada. Recebimentos parciais reduzem a parcela/saldo previsto correspondente.

Canceladas e encerradas sem captura saem do pipeline futuro, mas vendas e recebimentos históricos válidos permanecem nos relatórios do período. Arquivamento altera a visibilidade operacional, não apaga fatos financeiros.

Filtros de período devem indicar sua base: identificação para novas iniciativas, data da venda para vendido, data do recebimento para realizado e vencimento para previsão de caixa. Pipeline e estimativa representam a posição atual filtrada por atributos; não fingir posição histórica sem mecanismo de snapshots.

## 6. Etapas, situação e atrasos

Fluxo padrão: Oportunidade identificada → Diagnóstico e avaliação → Preparação para venda → Disponível para comercialização → Em comercialização → Venda formalizada → Acompanhamento financeiro → Encerrada.

Separe etapa do processo de situação operacional (ativa, suspensa, cancelada, encerrada) e de alertas calculados (bloqueada, atrasada, recebimento vencido). Uma iniciativa pode estar simultaneamente em comercialização, bloqueada e atrasada. Arquivamento é atributo separado.

Etapas configuráveis por tipo de captura têm IDs e significados estáveis. Renomear etapa não pode alterar regras de negócio. Edição de fluxo cria versão; iniciativas existentes mantêm sua versão até migração explícita com mapeamento e registro.

Regras de transição:

- Disponível para comercialização exige data real de disponibilização e checklist obrigatório concluído ou exceção justificada.
- Venda formalizada exige venda válida; registrar venda parcial não deve mover toda a iniciativa automaticamente.
- Acompanhamento financeiro deve preservar visibilidade de ações e bloqueios pendentes.
- Encerramento com captura exige motivo, data, ausência de ações obrigatórias pendentes e saldo financeiro zerado. Casos residuais exigem tratamento explícito, não baixa silenciosa.
- Cancelamento ou encerramento sem captura com vendas/recebimentos exige tratamento dos registros financeiros antes da transição.
- Retrocesso de etapa ou reabertura exige justificativa e preserva histórico e eventos financeiros.

Ação atrasada: prazo vigente anterior a hoje e status diferente de concluída/cancelada. Marco atrasado: data prevista anterior a hoje e correspondente data real ausente, enquanto aplicável à iniciativa ativa. Recebimento vencido: parcela vencida com saldo positivo. Prazo de hoje não está vencido.

Use datas de negócio sem conversão acidental de fuso e timestamps de auditoria com fuso. A iniciativa é contada uma única vez no KPI “com atraso”, mesmo com várias ações/marcos vencidos. Registre separadamente atraso de ações, marcos e financeiro.

## 7. Módulos e navegação

Menu: Visão Executiva, Carteira de Iniciativas, Plano de Ação, Pipeline Kanban, Gestão Financeira, Administração, Backup e Configurações.

### Visão Executiva

KPIs: iniciativas totais e ativas, com atraso, bloqueadas, estimativa original e vigente, vendido, recebido pelo critério caixa, saldo a receber, potencial ainda não vendido, previsão de caixa e ações vencidas. Exiba contagem de iniciativas sem estimativa para revelar incompletude.

Gráficos: comparação de estimado/vendido/recebido sem empilhamento aditivo; pipeline por etapa; potencial por captura e unidade; recebimentos mensais; gargalos; próximas disponibilizações; ações críticas. Cada KPI deve ter definição acessível e abrir registros que compõem o valor.

Filtros por unidade, responsável, captura, categoria, etapa, situação e período com base explicitada. Mostre filtros ativos e opção de limpar. Dashboard, tabela e Kanban devem refletir a mesma base e permitir navegar entre visões preservando os filtros compatíveis.

### Carteira e detalhe

Tabela pesquisável por ID, título, responsável e unidade. Permitir seleção de colunas, ordenação e exportação CSV dos registros filtrados, preservando acentos e protegendo contra fórmulas injetadas.

Detalhe com abas Visão Geral, Plano de Ação, Financeiro, Histórico e Documentos. Referências documentais aceitam descrição, identificador e URL validada; não carregar anexos grandes nem executar conteúdo. Tratar textos importados como texto, prevenindo injeção de HTML.

### Plano de ação consolidado

Lista por responsável, iniciativa, prazo, status e prioridade, com alertas e atalhos de edição. Checklist por iniciativa adaptável à modalidade.

### Kanban

Cartões com título, tipo, unidade, responsável, estimativa vigente, disponibilização prevista, progresso operacional, situação e alertas. Movimentação usa as mesmas validações do formulário, registra histórico e oferece alternativa acessível por seleção de etapa. Não incluir arrastar e soltar apenas visual.

### Gestão financeira

Telas para estimativas e revisões, vendas, parcelas e recebimentos. Exibir conciliação por iniciativa/venda, saldo e atrasos. Permitir análises mensais e anuais respeitando as diferentes datas dos fatos.

## 8. Administração e matchcodes

Gerenciar tipos de captura, categorias/subcategorias, modelos/etapas de fluxo, situações, status de ações, unidades, áreas, responsáveis, prioridades, bloqueios e unidades de medida. Permitir cadastrar, renomear, ordenar, definir padrão, ativar e inativar, mantendo código interno estável.

Categorias/subcategorias devem respeitar seus vínculos; não permitir combinação incompatível. Inativados continuam legíveis em registros existentes, mas não entram em novos cadastros. Não excluir opções utilizadas nem remover seus vínculos históricos.

Rótulos de estados podem ser editáveis, mas estados com efeito lógico devem manter classificação semântica estável (ex.: concluída/cancelada). Novos estados exigem classificação. Atraso e outros alertas são calculados, não matchcodes manuais.

Responsáveis cadastrados na versão local são referências de gestão, não usuários autenticados. O módulo administrativo local não implementa segregação real de permissões.

## 9. Persistência e integridade

IndexedDB com transações para gravações relacionadas, tratamento de falhas e migrações versionadas sem apagar a base. Só confirmar salvamento após sucesso; em falha, preservar o formulário e mostrar orientação.

Evite sobrescrita silenciosa entre abas com versão por registro e detecção de conflito. Histórico local registra evento, entidade, instante, antes/depois e justificativa. Identificação do operador local é declarada e não constitui auditoria autenticada ou inviolável.

Backup integral JSON inclui versão do esquema, data de exportação e todas as entidades/configurações. Validar esquema, tipos, IDs únicos, integridade referencial, valores e dependências antes de alterar a base. Mostrar resumo e erros.

Importação inicial funciona por substituição integral, não mesclagem. Exigir confirmação, oferecer exportação de segurança e aplicar restauração de forma atômica; erro não pode deixar importação parcial. Rejeitar versão incompatível com mensagem clara. Não executar código presente no backup.

Mostre indicador “Base local neste navegador” e lembrete de backup. Não prometer proteção contra limpeza de dados do navegador, falha do dispositivo ou edição indevida do arquivo de backup.

## 10. Demonstração

Iniciar com base vazia e opção explícita de carregar demonstração. Nunca adicionar exemplos automaticamente em uma base existente.

Dados fictícios e identificados como demonstração: equipamentos móveis, sucata ferrosa/não ferrosa, inservíveis e ativos industriais; diferentes unidades e responsáveis fictícios, iniciativas sem estimativa, atrasadas, bloqueadas, vendas parciais, múltiplas vendas, recebimentos parciais e encerradas. Não sugerir que são informações reais da Vale.

## 11. Critérios de aceite

Verifique e relate resultados dos cenários essenciais:

1. Criar, editar, duplicar e arquivar iniciativa; recarregar mantendo dados.
2. Registrar ações/dependências; rejeitar ciclos; concluir e reabrir preservando histórico.
3. Reprogramar prazo conservando original; calcular atraso corretamente inclusive na data de hoje.
4. Revisar estimativa sem alterar original; distinguir zero e ausência de informação.
5. Registrar duas vendas e vários recebimentos parciais na mesma iniciativa sem duplicação nos KPIs.
6. Exemplo de conciliação: estimativa original de R$100 mil, vigente R$120 mil, vendas válidas de R$40 mil e R$30 mil e recebimentos de R$10 mil e R$15 mil resultam em vendido R$70 mil, recebido R$25 mil, saldo a receber R$45 mil e potencial ainda não vendido R$50 mil.
7. Impedir recebimento acima do saldo e testar estorno/cancelamento com rastreabilidade.
8. Validar etapas, venda parcial, encerramento e retrocesso justificado.
9. Criar modalidade, alterar fluxo com versionamento e inativar opção utilizada preservando registros.
10. Conferir filtros e bases de data, dashboard, tabela, Kanban e exportação.
11. Exportar e restaurar backup válido; rejeitar arquivo inválido sem alterar a base.
12. Usar base vazia, tratar falha de salvamento e conflito entre abas com mensagem clara.
13. Verificar teclado, contraste, responsividade e ausência de erros nos fluxos principais.

Não alegar testes não executados. Informar navegador e forma de execução usados na validação e pendências concretas.

## 12. Entrega

Entregue index.html completo e funcional, sem botões fictícios, trechos incompletos, wireframe ou simulação de persistência. Priorize consistência dos dados e fluxos essenciais antes de efeitos visuais.

Inclua instruções objetivas para abrir/servir, iniciar vazio, carregar exemplos, cadastrar iniciativa, registrar ações/vendas/recebimentos e fazer backup. Explique limitações locais e componentes necessários para versão compartilhada. Identifique pendências reais sem apresentar funcionalidades futuras como prontas.

O resultado deve apoiar a rotina da equipe e as decisões executivas sobre disponibilização, comercialização e captura financeira da carteira.