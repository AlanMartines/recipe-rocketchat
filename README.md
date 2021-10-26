# Franz Recipe Documentação / Visão Geral

A arquitetura extensível de Franz permite que os desenvolvedores adicionem qualquer serviço por meio de recipe. É ainda possível criar aplicativos autônomos que não requerem um servidor externo para servir o aplicativo.

# OLX
Recipe para [Rocket.Chat](https://pt-br.rocket.chat/) integração com [Franz 5](http://meetfranz.com)

## O que é um Recipe?
Recipes são módulos de nó simples que consistem em uma configuração mínima de um `package.json`, `index.js` e `webview.js`.

## Instalação
1. Para instalar uma nova integração, baixe a pasta de integração, por exemplo `whatsapp`.
2. Abra a pasta Franz Plugins em sua máquina (note que este diretório `dev` pode não existir ainda, e você deve criá-lo):
  * Mac: `~/Library/Application Support/Franz/recipes/dev/`
  * Windows: `%appdata%/Franz/recipes/dev/`
  * Linux: `~/.config/Franz/recipes/dev`
3. Copie a pasta `whatsapp` para o diretório de plug-ins
4. Reinicie o Franz

## Estrutura
Cada recipe precisa de uma estrutura de arquivo específica para ser detectada como uma recipe pelo Franz

* recipes/dev/`[NAME]`/
  * icon.svg
  * icon.png
  * index.js
  * package.json
  * webview.js
  * darkmode.css

### package.json
O package.json é estruturado como qualquer outro módulo de nó e permite configurar completamente o serviço.

```json
{
  "id": "modelo",
  "name": "Modelo",
  "version": "1.0.0",
  "description": "Modelo",
  "main": "index.js",
  "author": "Alan Martines <alancpmartines@hotmail.com>",
  "license": "MIT",
  "repository": "https://github.com/alanmartines/recipe-modelo",
  "config": {
    "serviceURL": "https://url.modelo.com/"
  }
}


```

Para obter mais informações sobre todos os sinalizadores de configuração fornecidos, verifique o [documentos de configuração](https://github.com/meetfranz/plugins/blob/master/docs/configuration.md).


### index.js
Este é o seu código de "back-end". No momento, as opções são muito limitadas e a maioria dos serviços não precisa de um tratamento personalizado aqui. Se o seu serviço for relativamente simples e tiver um URL estático, por exemplo, _messenger.com_, _`[TEAMID]`.slack.com_ or _web.skype.com_ tudo que você precisa fazer para devolver o Franz Class:

```js
// default integration (e.g messenger.com, ...)
module.exports = Franz => Franz;
```

Se o seu serviço pode ser hospedado em servidores personalizados, você pode validar o URL fornecido para detectar se é o seu servidor e não, por exemplo, google.com. Para habilitar a validação, você pode substituir a função `validateServer`
```js
// RocketChat integration
module.exports = Franz => class RocketChat extends Franz {
  async validateUrl(url) {
    try {
      const resp = await window.fetch(`${url}/api/info`, {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
      });
      const data = await resp.json();

      return Object.hasOwnProperty.call(data, 'version');
    } catch (err) {
      console.error(err);
    }

    return false;
  }
};
```

`validateServer` precisa retornar uma [`Promise`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise), caso contrário, a validação falhará.

### webview.js
O webview.js é o script real que será carregado no webview. Aqui você pode fazer o que quiser para integrar perfeitamente o serviço no Franz. Por conveniência, fornecemos um conjunto muito simples de funções para definir emblemas de mensagens não lidas (`Franz.setBadge ()`) e injetar arquivos CSS (`Franz.injectCSS ()`).


```js
// orat.io integration
module.exports = (Franz) => {
  function getMessages() {
    let direct = 0;
    let indirect = 0;
    const FranzData = document.querySelector('#FranzMessages').dataset;
    if (FranzData) {
      direct = FranzData.direct;
      indirect = FranzData.indirect;
    }

    Franz.setBadge(direct, indirect);
  }

  Franz.loop(getMessages);
}
```

Para obter mais informações sobre as funções fornecidas, verifique o [API docs](https://github.com/meetfranz/plugins/blob/master/docs/frontend_api.md).

### Icons
Para mostrar todos os ícones de serviço claramente na IU do Franz, exigimos um .svg e .png em 1024x1024px.

### Dark Mode
Você pode fornecer um tema de modo escuro personalizado para suas receitas apenas colocando `darkmode.css` em sua pasta de receitas. Uma vez que o `darkmode.css` existe, você pode habilitar o modo escuro nas configurações do serviço.

Recipe Dark Mode só é suportado por Franz 5.0.0-beta.19+

### Debugging
Para depurar sua integração de serviço, abra Franz e use o atalho `Cmd / Ctrl + Alt + Shift + i` para abrir as ferramentas de desenvolvedor de receitas.

### Deployment
A partir de Franz 5, as recipes podem ser implantadas globalmente por meio de nosso repositório central. Para listar seu plugin, crie um [issue] (https://github.com/meetfranz/plugins/issues) com a tag `deploy`, vincule ao seu repositório e escreva uma breve descrição do que sua receita faz.

# Franz 5 - Plugins/Recipes
[Franz 5](https://github.com/meetfranz/franz) plugins agora são chamados __recipes__.

## Como instalar um recipe
No Franz 5, os recipes agora são servidas por meio de um repositório central. Exceto para fins de desenvolvimento, você não precisa mais instalar manualmente uma receita.

__Para adicionar um novo serviço:__
* Vá para sua Franz 5 App
* Pesquise e adicione o serviço necessário
* E pronto, simples assim!

## Como desenvolver, testar e implantar recipes
[Leia a documentação](https://github.com/meetfranz/plugins/tree/master/docs)

## Examplos de Recipes
* [Allo](https://github.com/SiloCityLabs/recipe-franz-googleallo)
* [Android Messages](https://github.com/dweinber/franz-recipe-android-messages)
* [BaseMessage](https://github.com/tranduykhanh/base-message-vn-recipe)
* [Bitbucket](https://github.com/17number/franz-recipe-bitbucket)
* [Discord](https://github.com/meetfranz/recipe-discord)
* [Flowdock](https://github.com/meetfranz/recipe-flowdock)
* [Gitter.im](https://github.com/BrianGilbert/franz-recipe-gitter)
* [Gmail](https://github.com/meetfranz/recipe-gmail)
* [Grape](https://github.com/meetfranz/recipe-grape)
* [GroupMe](https://github.com/meetfranz/recipe-groupme)
* [Hangouts](https://github.com/meetfranz/recipe-hangouts)
* [Hangouts Chat](https://github.com/meetfranz/recipe-hangoutschat)
* [HipChat](https://github.com/meetfranz/recipe-hipchat)
* [Inbox by Gmail](https://github.com/meetfranz/recipe-inbox)
* [Keep](https://github.com/SiloCityLabs/recipe-franz-googlekeep)
* [Mattermost](https://github.com/meetfranz/recipe-mattermost)
* [Messenger](https://github.com/meetfranz/recipe-messenger)
* [MySMS](https://github.com/meetfranz/recipe-mysms)
* [Proton Mail](https://github.com/lukas-reineke/franz-recipe-proton-mail)
* [Rocket.Chat](https://github.com/meetfranz/recipe-rocketchat)
* [Skype](https://github.com/meetfranz/recipe-skype)
* [Slack](https://github.com/meetfranz/recipe-slack)
* [Tawk.to](https://github.com/BrianGilbert/franz-recipe-tawk)
* [Telegram](https://github.com/meetfranz/recipe-telegram)
* [Todoist](https://github.com/avatarkava/recipe-todoist)
* [Tweetdeck](https://github.com/meetfranz/recipe-tweetdeck)
* [Twistapp.com](https://github.com/BrianGilbert/franz-recipe-twist)
* [Watson Workspace](https://github.com/edm00se/franz-recipe-watson-workspace)
* [WeChat](https://github.com/BrianGilbert/franz-recipe-wechat)
* [WhatsApp](https://github.com/meetfranz/recipe-whatsapp)
* [Zulip](https://github.com/adambirds/recipe-zulip)

## :rotating_light: Notícia importante
Franz 4 plugins __não são necessariamente compatíveis__ com Franz 5.