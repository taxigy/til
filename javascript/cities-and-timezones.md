# Where do I get a list of countries or timezones for a JS app?

Good question. You could use Google API to fetch list of addresses or whatever
per request, but let's do it greener. It's easy to have this data bundled into
your app directly.

## Countries

There's an NPM package,
[countries-list](https://www.npmjs.com/package/countries-list). Do

```bash
npm install --save countries-list
```

and then something like

```javascript
import countries from 'countries-list';

const citySelect = () => (
  <select>
    {Object.keys(countries.countries).map((key, index) => (
      <option value={countries.countries[key].name} />
    ))}
  </select>
);
```

## Languages

There's also a package that simply exports the list of all possible languages,
[langs](https://www.npmjs.com/package/langs).

Do

```bash
npm install --save langs
```

and then

```javascript
import langs from 'langs';

const languageSelect = () => (
  <select>
    {langs.names().map(lang => (
      <option value={lang} />
    ))}
  </select>
);
```

## Timezones

For timezones, there's a very light package,
[timezones.json](https://www.npmjs.com/package/timezones.json). I believe there
should be something more powerful, but this one is just fine.

```bash
npm install --save timezones.json
```

and

```javascript
import timezones from 'timezones.json';

const languageSelect = () => (
  <select>
    {timezones.map(({abbr, text}) => (
      <option value={abbr}>{text}</option>
    ))}
  </select>
);
```

