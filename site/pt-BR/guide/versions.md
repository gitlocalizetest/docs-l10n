# Compatibilidade de versão do TensorFlow

Este documento é para usuários que precisam de compatibilidade com versões anteriores do TensorFlow (para código ou dados) e para desenvolvedores que desejam modificar o TensorFlow, preservando a compatibilidade.

## Versão semântica 2.0

O TensorFlow segue o Semantic Versioning 2.0 ( [semver](http://semver.org) ) para sua API pública. Cada versão do TensorFlow possui o formato `MAJOR.MINOR.PATCH` . Por exemplo, o TensorFlow versão 1.2.3 possui a versão `MAJOR` 1, `MINOR` versão 2 e `PATCH` versão 3. As alterações em cada número têm o seguinte significado:

- **MAJOR**:  Potentially backwards incompatible changes.  Code and data that worked with a previous major release will not necessarily work with the new release. However, in some cases existing TensorFlow graphs and checkpoints may be migratable to the newer release; see [Compatibility of graphs and checkpoints](#compatibility_of_graphs_and_checkpoints) for details on data compatibility.

- **MENOR** : Recursos compatíveis com versões anteriores, aprimoramentos de velocidade etc. O código e os dados que funcionaram com uma versão secundária anterior *e* que dependem apenas da API pública não experimental continuarão a funcionar inalterados. Para detalhes sobre o que é e não é a API pública, consulte [O que é coberto](#what_is_covered) .

- **PATCH** : Correções de bugs compatíveis com versões anteriores.

Por exemplo, a versão 1.0.0 introduziu alterações *incompatíveis* com a versão 0.12.1. No entanto, a versão 1.1.1 era *compatível* com a versão 1.0.0. <a name="what_is_covered"></a>

## O que é coberto

Somente as APIs públicas do TensorFlow são compatíveis com versões anteriores nas versões secundária e de patch. As APIs públicas consistem em

- Todas as funções e classes documentadas do [Python](../api_docs/python) no módulo `tensorflow` e seus submódulos, exceto para

    - Símbolos privados: qualquer função, classe etc., cujo nome comece por `_`
    - Símbolos experimentais e `tf.contrib` , veja [abaixo](#not_covered) para detalhes.

    Observe que o código nos diretórios `examples/` and `tools/` não é acessível através do módulo Python `tensorflow` e, portanto, não é coberto pela garantia de compatibilidade.

    Se um símbolo estiver disponível através do módulo Python do `tensorflow` ou de seus submódulos, mas não estiver documentado, ele **não** será considerado parte da API pública.

- A API de compatibilidade (em Python, o módulo `tf.compat` ). Nas versões principais, podemos lançar utilitários e terminais adicionais para ajudar os usuários na transição para uma nova versão principal. Esses símbolos da API foram descontinuados e não são suportados (ou seja, não adicionaremos nenhum recurso e não corrigiremos bugs além de corrigir vulnerabilidades), mas eles se enquadram nas nossas garantias de compatibilidade.

- A [API C.](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/c/c_api.h)

- Os seguintes arquivos de buffer de protocolo:

    - [`attr_value`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/attr_value.proto)
    - [`config`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/protobuf/config.proto)
    - [`event`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/util/event.proto)
    - [`graph`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/graph.proto)
    - [`op_def`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/op_def.proto)
    - [`reader_base`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/reader_base.proto)
    - [`summary`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/summary.proto)
    - [`tensor`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/tensor.proto)
    - [`tensor_shape`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/tensor_shape.proto)
    - [`types`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/types.proto)

<a name="not_covered"></a>

## O que *não* é coberto

Algumas partes do TensorFlow podem mudar de maneiras incompatíveis com versões anteriores a qualquer momento. Esses incluem:

- **APIs experimentais** : para facilitar o desenvolvimento, isentamos alguns símbolos de API claramente marcados como experimentais das garantias de compatibilidade. Em particular, o seguinte não é coberto por nenhuma garantia de compatibilidade:

- qualquer símbolo no módulo `tf.contrib` ou em seus submódulos;
- qualquer símbolo (módulo, função, argumento, propriedade, classe ou constante) cujo nome contenha `experimental` ou `Experimental` ; ou
- qualquer símbolo cujo nome completo inclua um módulo ou classe que seja experimental. Isso inclui campos e submensagens de qualquer buffer de protocolo chamado `experimental` .

- **Outros idiomas** : APIs do TensorFlow em idiomas diferentes de Python e C, como:

- [C ++](../install/lang_c.md) (exposto através dos arquivos de cabeçalho no [`tensorflow/cc`](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/cc) ).
- [Java](../install/lang_java.md) ,
- [Ir](../install/lang_go.md)
- [Javascript](https://www.tensorflow.org/js)

- **Detalhes das operações compostas:** Muitas funções públicas no Python se expandem para várias operações primitivas no gráfico, e esses detalhes farão parte de qualquer gráfico salvo no disco como `GraphDef` s. Esses detalhes podem mudar para versões menores. Em particular, é provável que os testes de regressão que verificam a correspondência exata entre gráficos sejam interrompidos em versões menores, mesmo que o comportamento do gráfico deva ser inalterado e os pontos de verificação existentes ainda funcionem.

- **Detalhes numéricos de ponto flutuante:** Os valores específicos de ponto flutuante calculados pelas operações podem mudar a qualquer momento. Os usuários devem confiar apenas na precisão aproximada e na estabilidade numérica, não nos bits específicos calculados. Alterações em fórmulas numéricas em versões menores e de patch devem resultar em precisão comparável ou aprimorada, com a ressalva de que, no aprendizado de máquina, a precisão aprimorada de fórmulas específicas pode resultar em diminuição da precisão para o sistema geral.

- **Números aleatórios:** os números aleatórios específicos calculados podem mudar a qualquer momento. Os usuários devem confiar apenas nas distribuições aproximadamente corretas e na força estatística, não nos bits específicos calculados. Consulte o guia de [geração de números aleatórios](random_numbers.ipynb) para obter detalhes.

- **Inclinação de versão no Tensorflow distribuído: A** execução de duas versões diferentes do TensorFlow em um único cluster não é suportada. Não há garantias sobre a compatibilidade com versões anteriores do protocolo de conexão.

- **Bugs:** Reservamo-nos o direito de fazer alterações de comportamento incompatível com versões anteriores (embora não API) se a implementação atual estiver claramente quebrada, ou seja, se contradizer a documentação ou se um comportamento pretendido bem conhecido e bem definido não for implementado adequadamente devido para um bug. Por exemplo, se um otimizador alegar implementar um algoritmo de otimização conhecido, mas não corresponder a esse algoritmo devido a um bug, corrigiremos o otimizador. Nossa correção pode quebrar o código, dependendo do comportamento incorreto da convergência. Anotaremos essas alterações nas notas de versão.

- **Comportamento de erro:** podemos substituir erros por comportamento sem erro. Por exemplo, podemos alterar uma função para calcular um resultado em vez de gerar um erro, mesmo se esse erro estiver documentado. Também nos reservamos o direito de alterar o texto das mensagens de erro. Além disso, o tipo de erro pode ser alterado, a menos que o tipo de exceção para uma condição de erro específica seja especificado na documentação. <a name="compatibility_of_graphs_and_checkpoints"></a>

## Compatibilidade de modelos, gráficos e pontos de verificação salvos

SavedModel é o formato de serialização preferido para usar nos programas TensorFlow. Os Modelos salvos contêm duas partes: um ou mais gráficos codificados como `GraphDefs` e um ponto de verificação. Os gráficos descrevem o fluxo de dados das operações a serem executadas e os pontos de verificação contêm os valores de tensores salvos das variáveis em um gráfico.

Muitos usuários do TensorFlow criam SavedModels e os carregam e executam com uma versão posterior do TensorFlow. De acordo com o [semver](https://semver.org) , SavedModels gravados com uma versão do TensorFlow podem ser carregados e avaliados com uma versão posterior do TensorFlow com a mesma versão principal.

We make additional guarantees for *supported* SavedModels. We call a SavedModel which was created using **only non-deprecated, non-experimental, non-compatibility APIs** in TensorFlow major version `N` a <em data-md-type="emphasis">SavedModel supported in version `N`</em>. Any SavedModel supported in TensorFlow major version `N` can be loaded and executed with TensorFlow major version `N+1`. However, the functionality required to build or modify such a model may not be available any more, so this guarantee only applies to the unmodified SavedModel.

Nos esforçaremos para preservar a compatibilidade com versões anteriores o maior tempo possível, para que os arquivos serializados sejam utilizáveis por longos períodos de tempo.

### Compatibilidade com GraphDef

Os gráficos são serializados através do buffer do protocolo `GraphDef` . Para facilitar as alterações incompatíveis com os versões anteriores dos gráficos, cada `GraphDef` possui um número de versão separado da versão do TensorFlow. Por exemplo, `GraphDef` versão 17 obsoleto o `inv` op em favor da `reciprocal` . A semântica é:

- Each version of TensorFlow supports an interval of `GraphDef` versions. This interval will be constant across patch releases, and will only grow across minor releases.  Dropping support for a `GraphDef` version will only occur for a major release of TensorFlow (and only aligned with the version support guaranteed for SavedModels).

- Gráficos recém-criados recebem o número da versão mais recente do `GraphDef` .

- Se uma determinada versão do TensorFlow suportar a versão `GraphDef` de um gráfico, ela será carregada e avaliada com o mesmo comportamento que a versão TensorFlow usada para gerá-la (exceto os detalhes numéricos de ponto flutuante e números aleatórios, conforme descrito acima), independentemente dos principais versão do TensorFlow. Em particular, um GraphDef que seja compatível com um arquivo de ponto de verificação em uma versão do TensorFlow (como é o caso de SavedModel) permanecerá compatível com esse ponto de verificação nas versões subsequentes, desde que o GraphDef seja suportado.

    Observe que isso se aplica apenas aos gráficos serializados no GraphDefs (e SavedModels): o *código* que lê um ponto de verificação pode não ser capaz de ler os pontos de verificação gerados pelo mesmo código executando uma versão diferente do TensorFlow.

- If the `GraphDef` *upper* bound is increased to X in a (minor) release, there will be at least six months before the *lower* bound is increased to X.  For example (we're using hypothetical version numbers here):

    - TensorFlow 1.2 might support `GraphDef` versions 4 to 7.
    - O TensorFlow 1.3 pode adicionar o `GraphDef` versão 8 e oferecer suporte às versões 4 a 8.
    - Pelo menos seis meses depois, o TensorFlow 2.0.0 poderia descartar o suporte para as versões 4 a 7, deixando apenas a versão 8.

    Observe que, como as versões principais do TensorFlow geralmente são publicadas com mais de 6 meses de intervalo, as garantias dos Modelos salvos suportados detalhados acima são muito mais fortes do que as 6 meses de garantia dos GraphDefs.

Por fim, quando o suporte para uma versão do `GraphDef` for descartado, tentaremos fornecer ferramentas para a conversão automática de gráficos em uma versão mais recente do `GraphDef` suportada.

## Compatibilidade com gráficos e pontos de verificação ao estender o TensorFlow

Esta seção é relevante apenas ao fazer alterações incompatíveis no formato `GraphDef` , como adicionar operações, remover operações ou alterar a funcionalidade das operações existentes. A seção anterior deve ser suficiente para a maioria dos usuários.

<a id="backward_forward"></a>

### Compatibilidade com versões anteriores e anteriores

Nosso esquema de versão possui três requisitos:

- **Compatibilidade com versões anteriores** para suportar o carregamento de gráficos e pontos de verificação criados com versões mais antigas do TensorFlow.
- **Encaminhe a compatibilidade** para suportar cenários em que o produtor de um gráfico ou ponto de verificação é atualizado para uma versão mais recente do TensorFlow antes do consumidor.
- Ative a evolução do TensorFlow de maneiras incompatíveis. Por exemplo, removendo operações, adicionando atributos e removendo atributos.

Note that while the `GraphDef` version mechanism is separate from the TensorFlow version, backwards incompatible changes to the `GraphDef` format are still restricted by Semantic Versioning.  This means functionality can only be removed or changed between `MAJOR` versions of TensorFlow (such as `1.7` to `2.0`). Additionally, forward compatibility is enforced within Patch releases (`1.x.1` to `1.x.2` for example).

To achieve backward and forward compatibility and to know when to enforce changes in formats, graphs and checkpoints have metadata that describes when they were produced. The sections below detail the TensorFlow implementation and guidelines for evolving `GraphDef` versions.

### Esquemas de versão de dados independentes

Existem diferentes versões de dados para gráficos e pontos de verificação. Os dois formatos de dados evoluem em taxas diferentes entre si e também em taxas diferentes do TensorFlow. Ambos os sistemas de versão são definidos em [`core/public/version.h`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/public/version.h) . Sempre que uma nova versão é adicionada, uma nota é adicionada ao cabeçalho detalhando o que mudou e a data.

### Dados, produtores e consumidores

Distinguimos entre os seguintes tipos de informações da versão dos dados:

- **produtores** : binários que produzem dados. Os produtores têm uma versão ( `producer` ) e uma versão mínima do consumidor com os quais são compatíveis ( `min_consumer` ).
- **consumidores** : binários que consomem dados. Os consumidores têm uma versão ( `consumer` ) e uma versão mínima do produtor com a qual são compatíveis ( `min_producer` ).

Each piece of versioned data has a [`VersionDef versions`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/framework/versions.proto) field which records the `producer` that made the data, the `min_consumer` that it is compatible with, and a list of `bad_consumers` versions that are disallowed.

By default, when a producer makes some data, the data inherits the producer's `producer` and `min_consumer` versions. `bad_consumers` can be set if specific consumer versions are known to contain bugs and must be avoided. A consumer can accept a piece of data if the following are all true:

- `consumer` > = `min_consumer` dos dados
- `producer` dos dados> = `min_producer` do consumidor
- `consumer` não está nos `bad_consumers` dos dados

Como produtores e consumidores vêm da mesma base de código do TensorFlow, [`core/public/version.h`](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/core/public/version.h) contém uma versão principal dos dados que é tratada como `producer` ou `consumer` dependendo do contexto, e tanto `min_consumer` quanto `min_producer` (necessários para produtores e consumidores, respectivamente) . Especificamente,

- Para versões do `GraphDef` , temos `TF_GRAPH_DEF_VERSION` , `TF_GRAPH_DEF_VERSION_MIN_CONSUMER` e `TF_GRAPH_DEF_VERSION_MIN_PRODUCER` .
- Para versões de ponto de verificação, temos `TF_CHECKPOINT_VERSION` , `TF_CHECKPOINT_VERSION_MIN_CONSUMER` e `TF_CHECKPOINT_VERSION_MIN_PRODUCER` .

### Adicione um novo atributo com padrão a uma operação existente

Seguir as orientações abaixo fornece compatibilidade direta somente se o conjunto de operações não tiver sido alterado:

1. Se a compatibilidade para a frente é desejado, definir `strip_default_attrs` para `True` durante a exportação do modelo usando o `tf.saved_model.SavedModelBuilder.add_meta_graph_and_variables` e `tf.saved_model.SavedModelBuilder.add_meta_graph` métodos da `SavedModelBuilder` classe, ou `tf.estimator.Estimator.export_saved_model`
2. Isso retira os atributos com valor padrão no momento da produção / exportação dos modelos. Isso garante que o `tf.MetaGraphDef` exportado não contenha o novo atributo op quando o valor padrão for usado.
3. Ter esse controle pode permitir que consumidores desatualizados (por exemplo, servindo binários atrasados em relação aos binários de treinamento) continuem carregando os modelos e evitando interrupções na veiculação de modelos.

### Evolução das versões do GraphDef

Esta seção explica como usar esse mecanismo de controle de versão para fazer diferentes tipos de alterações no formato `GraphDef` .

#### Adicionar um op

Adicione a nova operação aos consumidores e produtores ao mesmo tempo e não altere nenhuma versão do `GraphDef` . Esse tipo de alteração é automaticamente compatível com versões anteriores e não afeta o plano de compatibilidade direta, pois os scripts de produtores existentes não usarão repentinamente a nova funcionalidade.

#### Adicione um op e alterne os wrappers Python existentes para usá-lo

1. Implemente a nova funcionalidade do consumidor e aumente a versão do `GraphDef` .
2. Se for possível fazer com que os wrappers usem a nova funcionalidade apenas nos casos que não funcionavam antes, os wrappers poderão ser atualizados agora.
3. Change Python wrappers to use the new functionality. Do not increment `min_consumer`, since models that do not use this op should not break.

#### Remover ou restringir a funcionalidade de um op

1. Corrija todos os scripts do produtor (não o próprio TensorFlow) para não usar a operação ou funcionalidade proibida.
2. Incremente a versão do `GraphDef` e implemente a nova funcionalidade do consumidor que proíbe a operação ou funcionalidade removida dos GraphDefs na nova versão e acima. Se possível, faça com que o TensorFlow pare de produzir `GraphDefs` com a funcionalidade proibida. Para fazer isso, adicione o [`REGISTER_OP(...).Deprecated(deprecated_at_version, message)`](https://github.com/tensorflow/tensorflow/blob/b289bc7a50fc0254970c60aaeba01c33de61a728/tensorflow/core/ops/array_ops.cc#L1009) .
3. Aguarde uma versão principal para fins de compatibilidade com versões anteriores.
4. Aumente `min_producer` para a versão GraphDef de (2) e remova completamente a funcionalidade.

#### Alterar a funcionalidade de um op

1. Adicione um novo op similar chamado `SomethingV2` ou similar e passe pelo processo de adicioná-lo e alternar os wrappers existentes do Python para usá-lo. Para garantir a compatibilidade direta, use as verificações sugeridas em [compat.py](https://www.tensorflow.org/code/tensorflow/python/compat/compat.py) ao alterar os wrappers do Python.
2. Remova a operação antiga (só pode ocorrer com uma alteração de versão principal devido à compatibilidade com versões anteriores).
3. Aumente `min_consumer` para descartar os consumidores com o antigo op, adicione novamente o antigo op como um alias para o `SomethingV2` e siga o processo para alternar os wrappers existentes do Python para usá-lo.
4. Siga o processo para remover o `SomethingV2` .

#### Proibir uma única versão insegura para o consumidor

1. Bump a versão `GraphDef` e adicione a versão ruim para `bad_consumers` para todos os novos GraphDefs. Se possível, adicione a `bad_consumers` apenas para GraphDefs que contêm uma certa op ou similar.
2. Se os consumidores existentes tiverem uma versão ruim, empurre-os para fora o mais rápido possível.
