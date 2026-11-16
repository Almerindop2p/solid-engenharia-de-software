# SOLID — princípios adotados neste projeto

Este projeto define os **cinco princípios SOLID** (SRP, OCP, LSP, ISP e DIP). Aplicá-los de modo incremental e proporcional à necessidade comprovada, preservando a arquitetura e o comportamento existente.

## 1. SRP — Single Responsibility Principle

**Definição operacional:** um módulo deve reunir o comportamento associado a uma responsabilidade coesa e a um motivo de mudança bem definido.

### Aplicação

1. Identificar por que um módulo precisaria mudar.
2. Se requisitos de atores ou regras independentes exigirem mudanças distintas no mesmo módulo, avaliar a separação.
3. Manter validação, acesso a dados e interface separados quando isso refletir limites reais do sistema.
4. Evitar fragmentar código por número de linhas: uma função pequena não exige, por si, uma classe nova.
5. Validar que uma separação não mudou o comportamento externo.

**Antipadrão:** concentrar apresentação, persistência, regras fiscais e autenticação em uma única classe por conveniência.

## 2. OCP — Open/Closed Principle

**Definição operacional:** módulos estáveis devem permitir que comportamentos sejam estendidos por pontos de variação definidos sem exigir modificações recorrentes no seu núcleo.

### Aplicação

1. Identificar a nova variação solicitada e os casos que devem continuar exatamente iguais.
2. Examinar se já existe interface, contrato, extensão por composição, estratégia, plug-in, adapter, handler ou tabela de registro.
3. Se houver um ponto de extensão adequado, implementar a nova variação ali.
4. Se não houver, comparar uma alteração pontual simples com a criação de uma abstração. Adotar a menos complexa que atenda ao requisito real.
5. Evitar cadeias de `if`/`switch` distribuídas por vários módulos para distinguir as mesmas variantes. Um ponto central de seleção pode ser perfeitamente apropriado.
6. Testar o novo comportamento e os existentes antes de concluir.

### Exemplo conceitual — emissão de notas fiscais

**Cenário:** o projeto já oferece emissão de NFS-e e precisa suportar NF-e de produtos.

- Preservar o fluxo existente de NFS-e e suas regras específicas.
- Se o projeto já possui um contrato de emissor e um mecanismo de escolha do tipo de nota, criar um emissor NF-e compatível e registrá-lo.
- Não duplicar a validação comum indiscriminadamente; não compartilhar regras fiscais que sejam distintas.
- Se o projeto não possui esse contrato, não introduzir automaticamente uma hierarquia complexa só por causa de dois casos.
- Permitir alterações pontuais no roteador ou na configuração quando necessárias para conectar a extensão.
- Verificar que notas de serviço continuam se comportando como antes e que notas de produto seguem suas validações próprias.

### Checklist de aceitação

- A nova funcionalidade foi adicionada sem regressão nos casos existentes?
- Foi reutilizado um mecanismo de extensão já existente, caso adequado?
- Houve alteração de código estável? Se sim, foi pequena e justificada?
- A solução evita condições duplicadas e dependências desnecessárias?
- As abstrações introduzidas atendem a uma necessidade presente, e não hipotética?
- Os testes ou a verificação manual confirmam o comportamento esperado?

**Detalhes:** consultar [ocp.md](ocp.md) para engenharia reversa de pontos de extensão, regras práticas e critérios de aceitação.

## 3. LSP - Liskov Substitution Principle

**Definição operacional:** qualquer implementação que promete o contrato de uma abstração deve poder substituí-la sem romper garantias observáveis dos consumidores.

### Aplicação

1. Examinar a abstração, seus consumidores e as implementações existentes.
2. Confirmar que subtipos não exigem pré-condições mais fortes nem oferecem pós-condições mais fracas.
3. Preservar invariantes, semântica das saídas, erros e efeitos colaterais definidos no contrato.
4. Não fingir substituibilidade entre tipos com capacidades ou entradas incompatíveis: especializar contratos quando necessário.
5. Verificar implementações intercambiáveis com testes de contrato compartilhados e testes dos consumidores.
6. Evitar hierarquias e interfaces desnecessárias; escolher a menor mudança segura.

**Exemplo conceitual:** se uma interface de emissão fiscal prometer aceitar qualquer documento fiscal, não declarar NFS-e e NF-e substitutos universais quando cada uma aceita um conjunto diferente de documentos. Modelar os contratos reais ou explicitar capacidades, conforme a arquitetura existente.

**Detalhes:** consultar [lsp.md](lsp.md) para regras de contratos, sinais de violação, exemplo em TypeScript e checklist de validação.

## 4. ISP - Interface Segregation Principle

**Definição operacional:** consumidores não devem ser obrigados a depender de métodos ou capacidades que não utilizam. Uma interface deve representar responsabilidades coesas de um papel ou capacidade real, sem forçar implementações artificiais.

### Aplicação

1. Identificar contratos, consumidores, implementadores, mocks e chamadas reais antes de dividir interfaces.
2. Mapear quais operações cada consumidor utiliza e procurar dependências desnecessárias com evidências concretas.
3. Evitar implementações com métodos vazios ou exceções de operação não suportada apenas para cumprir um contrato amplo.
4. Se necessário, definir interfaces menores por papel ou capacidade, compondo-as para consumidores que realmente exigem mais de uma.
5. Não impor interfaces de um método como padrão e não dividir contratos coesos só pelo número de operações.
6. Preservar a compatibilidade com consumidores existentes e os contratos verificados pelo LSP; testar casos modificados.

**Exemplo conceitual:** um componente que apenas consulta DAS não deve depender de um contrato que também obriga a implementar emissão de NFS-e e NF-e. Segregar a capacidade de consulta se a análise do projeto confirmar a necessidade, sem supor que os emissores sejam intercambiáveis.

**Detalhes:** consultar [isp.md](isp.md) para procedimento de engenharia reversa, matriz de consumidores, exemplo em TypeScript e checklist de validação.

## 5. DIP - Dependency Inversion Principle

**Definição operacional:** regras e casos de uso de alto nível não devem depender diretamente de detalhes técnicos instáveis; ambos devem depender de abstrações apropriadas. As abstrações não devem depender dos detalhes; os detalhes devem satisfazer os contratos.

### Aplicação

1. Rastrear o fluxo do caso de uso e identificar imports, chamadas, instâncias concretas, integrações e o ponto de composição.
2. Verificar se a dependência direta gera custo real de manutenção, teste ou substituição de infraestrutura. Não criar contratos hipotéticos.
3. Reutilizar abstrações e mecanismos de injeção existentes; se necessário, definir contrato mínimo conforme a necessidade do consumidor.
4. Conectar a implementação concreta na borda da aplicação, evitando que a regra de negócio importe o adaptador ou tipos do SDK.
5. Preservar contratos observáveis, autenticação, falhas, efeitos colaterais, segurança e compatibilidade.
6. Testar comportamento de alto nível com uma implementação substituta e as integrações reais separadamente, quando viável.

**Exemplo conceitual:** se um caso de uso precisa consultar situação cadastral de um CNPJ, avaliar um contrato de consulta independente do cliente HTTP apenas quando esse desacoplamento trouxer benefício real. A integração existente fornece a implementação do contrato; o caso de uso não precisa conhecer o SDK. Isso não implica alterar critérios fiscais ou criar uma API fictícia.

**Detalhes:** consultar [dip.md](dip.md) para engenharia reversa, implementação em TypeScript, distinção entre DIP e DI e critérios de aceitação.

## Relação entre SRP, OCP, LSP, ISP e DIP

O **SRP** define *onde* uma responsabilidade deve ficar; o **OCP** orienta *como* introduzir novas variantes sem espalhar alterações no sistema; o **LSP** verifica *se* as variantes respeitam o contrato das abstrações que prometem implementar; o **ISP** ajuda a definir *quais capacidades* cada consumidor realmente precisa conhecer; e o **DIP** orienta *em qual direção* as dependências devem apontar para proteger regras de alto nível dos detalhes técnicos. Aplicar os cinco com pragmatismo, sem remodelar código que já atende ao requisito.
