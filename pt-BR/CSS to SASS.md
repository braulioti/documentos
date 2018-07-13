# [Convertendo projeto JHipster CSS para SASS](http://braulioti.com.br)

[![Twitter: @braulio_info](https://img.shields.io/badge/contact-@braulio_info-blue.svg?style=flat)](https://twitter.com/braulio_info)

- Altere o nome de todos os arquivos e pastas css do projeto para scss
- Altere as referências dos arquivos css para scss dentro dos arquivos `.component.ts`
- Adicione ao .gitignore `/src/main/webapp/content/css/main.css`
- Crie um arquivo `/src/postcss.config.js` na raiz do projeto com o seguinte conteúdo:

```javascript
module.exports = {
    plugins: []
}
```

- Altere os arquivos descritos abaixo:

## /.angular-cli.json

```json
{
    ...
    "apps": [{
        ...
        "styles": [
            "content/scss/vendor.scss",
            "content/scss/global.scss"
        ],
    }],
    "defaults": {
        "styleExt": "scss",
        ...
    }
}
```

## /.yo-rc.json

```json
{
    ...
    "useSass": true,
    ...
}
```

## /package.json

```json
{
  "devDependencies": {
    ...
    "sass-loader": "6.0.6",
    "node-sass": "4.5.3",
    "postcss-loader": "6.0.23",
    ...  
  }
}
```

## /pom.xml

```xml
    ...
    <execution>
        <id>yarn install</id>
        <goals>
            <goal>yarn</goal>
        </goals>
        <configuration>
            <arguments>install --force</arguments>
        </configuration>
    </execution>
    ...
```

## /src/main/webapp/app/vendor.ts
```typescript
import '../content/scss/vendor.scss';
```

## /webpack/webpack.dev.js
```javascript
{
    ...
    module: {
        rules: [{
            test: /\.ts$/,
            enforce: 'pre',
            loaders: 'tslint-loader',
            exclude: ['node_modules', new RegExp('reflect-metadata\\' + path.sep + 'Reflect\\.ts')]
        },
        {
            test: /\.ts$/,
            loaders: [
                'angular2-template-loader',
                'awesome-typescript-loader'
            ],
            exclude: ['node_modules/generator-jhipster']
        },
        {
            test: /\.scss$/,
            loaders: ['to-string-loader', 'css-loader', 'sass-loader'],
            exclude: /(vendor\.scss|global\.scss)/
        },
        {
            test: /(vendor\.scss|global\.scss)/,
            loaders: ['style-loader', 'css-loader', 'postcss-loader', 'sass-loader']
        },
        {
            test: /\.css$/,
            loaders: ['to-string-loader', 'css-loader'],
            exclude: /(vendor\.css|global\.css)/
        },
        {
            test: /(vendor\.css|global\.css)/,
            loaders: ['style-loader', 'css-loader']
        }]
    },
    ...
}
```
## /webpack/webpack.prod.js
```javascript
...
const extractSASS = new ExtractTextPlugin(`[name]-sass.[hash].css`);
...
module.exports = webpackMerge(commonConfig({ env: ENV }), {
    ...
    entry: {
        ...
        global: './src/main/webapp/content/scss/global.scss',
        ...
    }
    module: {
        ...
        {
            test: /\.scss$/,
            loaders: ['to-string-loader', 'css-loader', 'sass-loader'],
            exclude: /(vendor\.scss|global\.scss)/
        },
        {
            test: /(vendor\.scss|global\.scss)/,
            use: extractSASS.extract({
                fallback: 'style-loader',
                use: ['css-loader', 'postcss-loader', 'sass-loader']
            })
        },
        ...
    },
    plugins: [
        extractSASS,
        ...
    ]
}
```

## Autor
- Email: braulio@braulioti.com.br
- Twitter: http://twitter.com/braulio_info
- GitHub: https://github.com/braulioti
- Website: http://braulioti.com.br
