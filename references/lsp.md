# LSP - Principio da Substituição de Liskov

## Definição operacional

Uma implementação especializada deve poder substituir a abstração que declara cumprir, nos pontos em que essa abstração é esperada, **sem violar contratos observáveis**. Isso se aplica a herança, implementação de interfaces, polimorfismo, adaptadores e composição que prometem o mesmo contrato.

**Não confundir:** LSP não exige resultados idênticos para todas as implementações, apenas a preservação das garantias que o consumidor pode legitimamente esperar. Uma semelhança entre nomes de classes não prova substituibilidade.

## Inspecionar o contrato antes de propor mudanças

1. Localizar a classe-base, interface, tipo ou protocolo e **todos os seus consumidores e implementadores relevantes**.
2. Identificar o contrato efetivo a partir de tipos, código, documentação, chamadas reais e testes. Não pressupor garantias não demonstradas.
3. Descrever: entradas aceitas, saídas, significado dos resultados, erros, estado, persistência, efeitos colaterais e propriedades temporais (quando relevantes).
4. Comparar implementações com o contrato: cada uma o cumpre para **todos os casos que a abstração promete suportar**?
5. Identificar condicionais nos consumidores que inspecionam o subtipo ou contornam exceções. Investigar se indicam violação real ou apenas uma variação permitida.
6. Preferir a menor correção segura: ajustar implementação, esclarecer contrato, especializar interfaces ou empregar composição; não criar hierarquias por princípio.
7. Executar a mesma bateria de testes de contrato para cada implementação intercambiável e testar consumidores representativos.

## Regras que a implementação deve cumprir

| Propriedade | Critério de compatibilidade | Possível violação |
| --- | --- | --- |
| Pré-condições | Não exigir mais do que o contrato-base exige. | Uma subclasse rejeita entradas válidas permitidas pela interface. |
| Pós-condições | Preservar ou fortalecer garantias prometidas, sem enfraquecê-las. | Retornar sucesso sem realizar a operação prometida. |
| Invariantes | Preservar propriedades de estado estabelecidas pelo contrato. | Deixar estado inválido após a operação. |
| Semântica das saídas | Preservar significado, formato e restrições documentadas. | Retornar identificador local onde se prometeu protocolo externo. |
| Erros | Respeitar falhas e garantias de recuperação previstas; não acrescentar surpresas incompatíveis. | Lançar erro novo não previsto para um caso válido. |
| Efeitos colaterais | Respeitar autorização, persistência, ordem e idempotência quando fizerem parte do contrato. | Duplicar emissões em uma operação prometida como idempotente. |
| Tempo e assincronia | Preservar garantias de conclusão, cancelamento e entrega quando especificadas. | Resolver a promessa antes do envio que o contrato exige concluir. |

**Nota:** não transformar toda diferença de desempenho, exceção ou efeito colateral em violação automática. Verificar primeiro quais garantias pertencem de fato ao contrato.

## Exemplo hipotético: emissão de NFS-e e NF-e

**Contrato excessivamente amplo (risco de LSP):**

```ts
interface EmissorFiscal {
  emitir(documento: DocumentoFiscal): Promise<ResultadoEmissao>;
}
```

Se `DocumentoFiscal` inclui tanto produtos quanto serviços, mas `EmissorNFSe` rejeita todos os documentos de produto e `EmissorNFe` rejeita todos os documentos de serviço, **nenhum deles pode substituir livremente esse `EmissorFiscal`** se a interface promete aceitar qualquer `DocumentoFiscal`.

**Opção com contratos especializados, quando o projeto permitir:**

```ts
interface Emissor<TDocumento> {
  emitir(documento: TDocumento): Promise<ResultadoEmissao>;
}

type EmissorServico = Emissor<DocumentoServico>;
type EmissorProduto = Emissor<DocumentoProduto>;
```

Cada implementação é substituível **somente nos consumidores que dependem de seu contrato compatível**. Um `EmissorServico` não vira automaticamente um `Emissor<DocumentoFiscal>`. Outra solução válida pode ser explicitar capacidades e selecionar o emissor antes de invocar um contrato específico. Escolher conforme a arquitetura real, sem inventar interfaces no projeto.

**Falha a evitar:** capturar uma rejeição por tipo incompatível e retornar falso sucesso para fazer a assinatura parecer compatível. Isso mascara a quebra do contrato.

## Testes de contrato

- Executar a **mesma suíte de casos esperados pela abstração** para cada implementação que afirma ser substituível.
- Cobrir entradas válidas e limites permitidos, saídas e semântica, erros esperados, invariantes, autorização e efeitos colaterais aplicáveis.
- Verificar consumidores que dependem da abstração, principalmente quando novos tipos forem introduzidos.
- Testar comportamentos assíncronos, retries e idempotência **quando fizerem parte do contrato**.
- Se não houver testes executáveis, documentar os casos e as limitações; não declarar que a substituição está validada.

## Critérios de aceitação

- [ ] A abstração tem contrato observável e escopo de entradas claramente definido?
- [ ] O subtipo aceita todas as entradas válidas prometidas sem adicionar pré-condições?
- [ ] Pós-condições, invariantes, erros e efeitos colaterais previstos foram preservados?
- [ ] Os consumidores usam a abstração sem desvios artificiais por classe concreta?
- [ ] Casos realmente incompatíveis foram modelados como contratos distintos, sem forçar substituição falsa?
- [ ] Testes compartilhados e testes dos consumidores relevantes foram executados ou suas limitações foram explicitadas?
- [ ] A mudança respeita SRP, OCP e o escopo solicitado sem adicionar abstrações especulativas?

**Regra final:** preferir um contrato honesto e mais específico a uma hierarquia aparentemente genérica que viole suas próprias promessas.
