# ETF 101

## Weltportfolio

Lorem Lorem.

## Steuern in Deutschland

[ETF und Steuern: das Investmentsteuergesetz](https://www.justetf.com/de/news/etf/etf-und-steuern-das-neue-investmentsteuergesetz-ab-2018.html)

### Thesaurierend

- keine Steuern während der Ansparphase
- Steuern werden fällig beim Verkauf
- 30% sind steuerfrei

#### Beispielrechnung: Depowert

|                                  | Betrag  |
| :------------------------------: | :-----: |
|          Einmal-Invest           | 30.000€ |
|      monatlich für 10 Jahre      | 1.000€  |
|  durchschnittliche Rendite p.a.  |  7,0%   |
| durchschnittliche Inflation p.a. |  2,5%   |

```js
const durchschnRendite = 0.07;
const einmalInvest = 30000;
const sparRate = 12 * 1000;
let summe = einmalInvest;
const laufzeit = 10;
for (let i = 0; i < laufzeit; i++) {
  summe = summe * (1 + durchschnRendite) + sparRate;
}
// Vorabpauschale wird durch Steuerpauschbetrag abgegolten
// daher reduziert sich die Rendite nicht
const inflation = 2.5 / 100;
const kaufkraft = summe / (1 + inflation) ** laufzeit;
const formatter = new Intl.NumberFormat('de-DE', {
  style: 'currency',
  currency: 'EUR',
});
console.log(
  '| ' + 'nach ' + laufzeit + ' Jahren |',
  formatter.format(summe) + ' |',
  formatter.format(kaufkraft) + ' |'
);
```

|                |    Depowert    |  Kaufkraft   |
| :------------: | :------------: | :----------: |
| nach 10 Jahren |  224.811,92 €  | 175.622,71 € |
| nach 20 Jahren |  608.036,44 €  | 371.066,97 € |
| nach 30 Jahren | 1.361.897,09 € | 649.274,47 € |

#### Beispielrechnung: Versteuerung bei Verkauf

```js
const laufzeit = 10;
const einzahlung = 30000 + 12 * 1000 * laufzeit;
const depotWert = 224811.92;
// Vorabpauschale: jährlich
const durchschnRendite = 0.07;
const durchschnBasisZins = 0.3 / 100;
const einmalInvest = 30000;
const sparRate = 12 * 1000;
const steuerRate = 0.7;
let summe = einmalInvest;
let summeVorabPauschale = 0;
for (let i = 0; i < laufzeit; i++) {
  // Basisertrag = ETF-Wert zum Jahresanfang x Basiszins x 0,7; mind. 0
  const basisertrag = summe * durchschnBasisZins * steuerRate;
  // Vorabpauschale = Basisertrag - Ausschüttungen; mind. 0
  const vorabPauschale = basisertrag - 0;
  summeVorabPauschale += vorabPauschale;
  // Besteuerung der Vorabpauschale: Vorabpauschale x 70% x 26,375%
  // Vorabpauschale kann bis 801€ durch Sparerpauschbetrag abgegolten werden
  //   const steuerVorabPausch = vorabPauschale * 0.7 * 0.26375;
  summe = summe * (1 + durchschnRendite) + sparRate;
}
// Abgeltungssteuer
const rendite = depotWert - einzahlung;
// Auf die Vorabpauschale wurde bereits Steuer gezahlt (abgegolten durch Freistellungsauftrag)
const zuVersteuern = (rendite - summeVorabPauschale) * steuerRate;
// Bei Verkauf: Abgeltungsteuer inkl. Solidaritätszuschlag
const abgeltungsSteuer = 26.375 / 100;
const steuer = zuVersteuern * abgeltungsSteuer;
// Nettoertrag
const nettoErtrag = depotWert - steuer;
const formatter = new Intl.NumberFormat('de-DE', {
  style: 'currency',
  currency: 'EUR',
});
console.log(
  '| Summe Vorabpauschale |',
  formatter.format(summeVorabPauschale) + ' |'
);
console.log(
  '| kompensierte Steuern auf Vorabpauschale |',
  formatter.format(summeVorabPauschale * steuerRate * abgeltungsSteuer) + ' |'
);
console.log('| Steuer |', formatter.format(steuer) + ' |');
console.log('| Nettoertrag |', formatter.format(nettoErtrag) + ' |');
```

|                                         |    Betrag    |
| :-------------------------------------: | :----------: |
|          Summe Vorabpauschale           |  2.244,36 €  |
| kompensierte Steuern auf Vorabpauschale |   414,36 €   |
|                 Steuer                  | 13.397,79 €  |
|               Nettoertrag               | 211.414,13 € |
