# Reutilização de métodos e funções (DRY pragmático)

## Objetivo

Reduzir ao máximo a quantidade de **código novo necessário** para atender ao pedido, preferindo reutilizar rotinas existentes. Preservar legibilidade, comportamento, segurança, manutenibilidade e testes. A contagem de linhas é uma pista de custo, não um objetivo isolado.

## Procedimento antes de escrever código

1. **Delimitar o escopo:** descrever qual comportamento novo é exigido e quais fluxos devem permanecer inalterados.
2. **Procurar no repositório:** pesquisar símbolos e conceitos relacionados; verificar funções, métodos, utilitários, serviços, hooks, bibliotecas e testes existentes. Inspecionar chamadas atuais, não apenas nomes semelhantes.
3. **Confirmar equivalência:** conferir parâmetros, tipos, valor retornado, validações, erros, permissões, estados, efeitos colaterais, assincronicidade e desempenho. Não reutilizar uma rotina com significado de negócio diferente.
4. **Escolher a alternativa menos complexa:** (a) chamar método existente; (b) compor funções existentes; (c) fazer pequena extensão compatível; (d) extrair trecho genuinamente duplicado; (e) criar nova função apenas se as anteriores não atenderem.
5. **Evitar abstrações prematuras:** não criar helpers, wrappers, classes, interfaces, bibliotecas ou parâmetros opcionais apenas para economizar poucas linhas. Não espalhar condições para disfarçar a duplicação.
6. **Examinar o diff:** reduzir o número de linhas novas, locais alterados e lógica repetida sem compactar comandos, remover validações ou prejudicar a compreensão.
7. **Validar o resultado:** rodar testes existentes, checagens de tipo e lint aplicáveis; verificar os fluxos consumidores e relatar limitações reais.

## Critérios para reutilizar

| Verificação | Decisão |
| --- | --- |
| A rotina já implementa a mesma regra e o mesmo contrato? | Reutilizar diretamente. |
| A rotina é quase igual, mas difere em uma opção prevista no domínio? | Considerar pequena extensão retrocompatível, se mais simples. |
| Existem duas cópias de uma regra realmente idêntica? | Considerar centralizar na camada apropriada, dentro do escopo. |
| A similaridade é só sintática e as regras divergem? | Manter separadas: não unificar por aparência. |
| A extração cria mais camadas, dependências ou complexidade? | Preferir a implementação simples e coesa. |

## Exemplo conceitual (TypeScript)

Se o projeto **já possui** `normalizarCnpj()` com o contrato correto, não repetir `replace()` e expressões regulares em cada tela:

**Antes:**

```ts
const cnpj = valorDigitado.replace(/\D/g, '');
```

**Depois, quando o contrato for compatível:**

```ts
const cnpj = normalizarCnpj(valorDigitado);
```

O exemplo não afirma que essa função existe em um projeto específico. Primeiro, localizar o módulo real, importar conforme suas convenções e conferir casos-limite. **Normalizar não é validar:** não remover a validação necessária por acreditar que as rotinas são equivalentes.

## Integração com SOLID

- **SRP:** reutilizar na responsabilidade correta, sem transformar um utilitário em módulo multifuncional.
- **OCP:** aproveitar pontos existentes de extensão em vez de duplicar a implementação do fluxo.
- **LSP:** manter contratos observáveis ao trocar implementações.
- **ISP:** não impor dependências extras para reaproveitar uma única operação.
- **DIP:** reutilizar contratos e adaptadores existentes, sem acoplar regras de negócio a infraestrutura por conveniência.

## Checklist de aceite

- [ ] Pesquisei implementações existentes antes de criar código?
- [ ] Confirmei equivalência funcional e compatibilidade?
- [ ] Evitei duplicar regras e criar arquivos/abstrações sem justificativa?
- [ ] O diff é pequeno, compreensível e restrito ao escopo?
- [ ] Preservei segurança, tratamento de erros e comportamento anterior?
- [ ] Executei as validações disponíveis e relatei o resultado?
