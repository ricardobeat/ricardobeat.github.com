---
layout: post
title: Debug de uma aplicação em CoffeeScript
---

{{ page.title }}
================

O tópico de debug surge com frequência em discussões sobre CoffeeScript. Neste post vou dar um exemplo real de erros utilizando CoffeeScript e a dificuldade (dica: nenhuma) em rastrear a origem.

Este é um projeto de front-end em CoffeeScript, não é dos mais extensos (~1200 linhas) mas suficiente como exemplo. A aplicação está separada em uma dúzia de arquivos:

![Estrutura de arquivos coffeescript](/images/debug-projeto.png)

Estes são compilados via `Cakefile` utilizando [flour](http://ricardobeat.github.com/cake-flour):

{% highlight coffeescript %}
task 'build:dev', ->
    flour.minifiers.js = null
    bundle [
        'scripts/util.coffee'
        'scripts/api.coffee'
        'scripts/modules.coffee'
        'scripts/user.coffee'
        'scripts/profile.coffee'
        'scripts/location.coffee'
        'scripts/activity.coffee'
        'scripts/stats.coffee'
        'scripts/main.coffee'
    ], 'resources/app.js'
{% endhighlight %}

Neste setup todos os arquivos `.coffee` são compilados e concatenados em um único arquivo `resources/app.js`, que é incluido no `<head>` do HTML. Durante o desenvolvimento a task `cake watch` se encarrega de re-compilar o JS a cada alteração.

Erros de sintaxe
----------------

Sabe aquele erro maldito de js que te faz voltar pro editor pra corrigir *um caractere*? Seja um ponto-e-vírgula no lugar errado, chave não fechada, vírgula, etc, esses erros *praticamente não existem* em coffeescript.

Em primeiro, porque a linguagem é muito simples e limpa, sem tantos caracteres de controle; segundo, porque a maioria desses errors são acusados na hora do compile, não da execução. Além disso as mensages de erro do compiler coffeescript são muito claras. Exemplo:

{% highlight coffeescript %}
class AppView extends Backbone.View

    ...

    start: ->

        @loadTemplates()
        @root = $('#main'))

{% endhighlight %}

Consegue ver o erro? No momento em que o arquivo for salvo, a task `watch` que está rodando no terminal acusa o erro:

![Erro de compilação](/images/debug-compile-error.png)

Basta ir até a linha em questão e remover o parêntese extra.

Erros de execução
-----------------

Os erros de execução são a questão que mais preocupa quem ainda não usou coffeescript: receio de não conseguir identificar a linha de código que gerou o erro, pelo fato de o stack trace ser indicado em cima do Javascript compilado, não do fonte.

Vamos ver o quão difícil é na prática. Como exemplo vou usar esta função de inicialização do app:

{% highlight coffeescript %}
class AppView extends Backbone.View

    initialize: ->
        @connect()
        @currentUser = new User.Model { key: session.get 'userkey' }

{% endhighlight %}

Agora vamos inserir um erro, uma tentativa de acesso a uma propriedade não-existente no objeto `User`:

{% highlight coffeescript %}

    initialize: ->
        @connect()
        @currentUser = new User.model { key: session.get 'userkey' }

{% endhighlight %}

(se você não percebeu o erro: `Model` -> `model`)

Nesse caso a compilação ocorre normalmente - para o compiler o código está ok, ele não tem conhecimento sobre os objetos no momento da execução. Ao abrir o browser porém a aplicação não carregou:

![Tela branca](/images/debug-blank.png)

`Uncaught TypeError: undefined is not a function [app.js:1450]`.

"*Linha 1450?? Jamais vou encontrar esse erro.*". Na prática o fluxo é o mesmo de debugar javascript. Abra o stack:

![Stack trace](/images/debug-stack.png)

A função culpada é `App.initialize`. É só abrir o arquivo `app.coffee`, encontrar a definição da função (por memória ou `Cmd+F initialize:`) e pronto! Se tu está boiando no projeto e não faz a menor idéia de em que arquivo a função está, bom... vai de `Find in project` ou pede ajuda pra um colega.

![Tela branca](/images/debug-file.png)

Source maps
-----------

Erros de lógica exigem reler/interpretar o trecho de código para encontrar o erro - portanto não há nenhuma necessidade de abrir o arquivo `app.js` compilado. Sendo um pré-processador, é por definição impossível que o compiler CoffeeScript gere erros de lógica (*impossível* nesse contexto == mais de 5200 linhas de testes). 

No exemplo acima, a única diferença em relação a debugar javascript é não termos a linha exata que gerou o erro. Chrome e Firefox já estão implementando uma feature chamada **source maps**, que permite ao console mapear logs à linha de código do arquivo fonte, tanto para javascript (coffeescript, typescript, etc) quanto css (LESS, Sass, Stylus, etc).

Infelizmente a versão atual do coffeescript (1.4.0) não gera source maps, mas um rewrite [já está em andamento](https://github.com/michaelficarra/CoffeeScriptRedux) e deve se tornar o compiler oficial nos próximos meses.


Node.js
-------

Ao instalar o coffeescript na sua máquina com `npm install coffee-script -g`, o executável `coffee` é adicionado ao seu path. Ele nada mais é do que uma invocação do `node` com o módulo `coffee-script` carregado. Ou seja, `coffee meuapp.coffee` é o equivalente a executar:

    require('coffee-script') // registra .coffee no require.extensions
    require('./meuapp')

Este padrão é comum no deployment de aplicações em serviços como Heroku, dotCloud, nodejitsu, etc - em geral se exige um arquivo `server/main/app.js` para inicialização.

Como exemplo vou usar o código do [jetcraft](http://playjetcraft.net/), jogo que desenvolvi junto com o [@vitor42](http://twitter.com/vitor42) para o Node Knockout 2013:

{% highlight coffeescript %}
# app.coffee
express  = require 'express'
http     = require 'http'

app = express()

app.configure ->
  app.use app.router
  app.use express.static "#{__dirname}/public"
  
app.configure 'development', ->
  app.use express.errorHandler dumpExceptions: true, showStack: true

app.get '/', (req, res) ->
  res.render 'index', nada

{% endhighlight %}

Note que a variável `nada` não foi definida. Como lá em cima ativamos o `express.errorHandler` com dump e showStack, o erro é enviado para browser além de impresso no console:

![Erro do Express no browser](/images/debug-node-browser.png)

![Erro no terminal](/images/debug-node-coffee.png)

Em ambos os casos o erro indica a linha exata do `.coffee`:

    ReferenceError: nada is not defined
        at /Users/ricardobeat/Projects/jetcraft/app.coffee:42:32

Portanto no node.js coffeescript e javascript estão no mesmo patamar. Não é necessário ter um script de build ou tarefas de compile/watch. Somente no caso de escrever código para ser compartilhado, como uma biblioteca ou módulo do NPM, é util pré-compilar o código para distribuição.

Conclusões
----------

Não tenha medo de utilizar CoffeeScript pela questão de debug. Se a sua equipe tem capacidade e curiosidade para aprender a linguagem, as vantagens que ela oferece são muitas. Ainda mais considerando o uso de TDD, build scripts (reduzindo o tamanho de cada source), e os padrões atuais de código, não ter a linha exata de um runtime error é a menor das preocupações.

Aprenda mais sobre CoffeeScript no [site oficial](http://coffeescript.org) ou com os livros (grátis):

- [The Little Book on CoffeeScript](http://arcturo.github.com/library/coffeescript/)
- [Smooth CoffeeScript](http://autotelicum.github.com/Smooth-CoffeeScript/interactive/interactive-coffeescript.html)

[Siga no twitter: @ricardobeat](http://twitter.com/ricardobeat)
