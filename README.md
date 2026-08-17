# Automação de Extração de Dados de Produção (ProdWin → Excel) com Power Automate Desktop

Fluxo de RPA (automação de processos) desenvolvido para eliminar a extração manual
de relatórios diários de produção do sistema interno **ProdWin**, consolidando os
dados automaticamente em uma planilha Excel mestre.

## Problema

A consolidação de dados de produção (OEE, paradas, apontamentos) era feita
manualmente: exportar relatório por relatório do sistema, dia por dia, e colar
os dados na planilha de controle. Um processo demorado e sujeito a erro humano
(linha trocada, data errada, cópia duplicada).

## Solução

Um fluxo em **Power Automate Desktop** que:

1. Recebe um intervalo de datas (início e fim)
2. Percorre automaticamente cada dia do período, formatando a data no padrão
   exigido pelo sistema
3. Acessa o ProdWin via navegador, preenche os campos de data e exporta o relatório
4. Aguarda a geração do arquivo e localiza o download mais recente
5. Abre o Excel exportado e valida se existem dados (`ValorTotal <> 0`) antes de prosseguir
6. Copia o intervalo de dados relevante e cola na primeira linha livre da planilha mestre
7. Registra a data de referência na coluna de controle
8. Repete o processo para o próximo dia até o fim do intervalo
9. Ao final, limpa os arquivos temporários baixados

## Como funciona (fluxograma simplificado)

```
Definir período (data início / data fim)
        │
        ▼
   Loop por dia ──────────────────────────┐
        │                                 │
   Formata data (dd/MM/yyyy)              │
        │                                 │
   Acessa ProdWin → exporta relatório     │
        │                                 │
   Abre Excel exportado                   │
        │                                 │
   Tem dados? ──Não──► pula para próximo ─┘
        │
       Sim
        │
        ▼
   Copia dados → cola na planilha mestre
   (próxima linha livre disponível)
        │
        ▼
   Próximo dia do loop
        │
        ▼
   Fim: limpa arquivos temporários
```

## Variáveis principais do fluxo

| Variável | Função |
|---|---|
| `DataAtual` / `DataFinal` | Intervalo de datas do relatório a ser extraído |
| `LoopIndex` | Contador do loop, percorre cada dia do período |
| `DataLoop` | Data do dia atual dentro do loop (`DataAtual + LoopIndex` dias) |
| `DataFormatada` | Data convertida para o formato `dd/MM/yyyy` exigido pelo sistema |
| `ArquivoBaixado` | Referência ao arquivo Excel exportado do sistema |
| `ValorTotal` | Valor lido da planilha exportada, usado para validar se há dados no dia |
| `PrimeiraLinhaVazia` / `LinhaAtual` | Controle de onde inserir os novos dados na planilha mestre |
| `ExcelInstance` / `ExcelBase` | Instâncias do Excel controladas pelo fluxo (arquivo exportado x planilha mestre) |

## Resultado

- Eliminou a extração e colagem manual de relatórios diários
- Reduziu o risco de erro humano (linha errada, data trocada, duplicidade)
- Fluxo executado sob demanda, cobrindo qualquer intervalo de datas necessário
  em uma única execução

## Ferramentas utilizadas

- Power Automate Desktop (automação de interface web e Excel)
- Manipulação de datas, loops e condicionais
- Integração via automação de navegador (Microsoft Edge)

---

**Autor:** Lucas Keler Batista
Projeto documentado para fins de portfólio. Os dados, caminhos e sistemas
internos da empresa foram omitidos ou ocultados por questão de confidencialidade.
