---
title: 'Bitcoin Optech Newsletter #197'
permalink: /de/newsletters/2022/04/27/
name: 2022-04-27-newsletter
slug: 2022-04-27-newsletter
type: newsletter
layout: newsletter
lang: de
---

Der Newsletter dieser Woche fasst die Diskussion über die Aktivierung von
`OP_CHECKTEMPLATEVERIFY` zusammen und beinhält unsere regelmäßigen Rubriken
über herausragende Fragen und Antworten auf Bitcoin Stack Exchange, neue
Software-Versionen und Release-Kandidaten, und jüngste Änderungen an gängiger
Bitcoin Infrastruktur Software.

## News

- **Diskussion über die Aktivierung von CTV:** Jeremy Rubin [schrieb][rubin
  ctv-st] der Bitcoin-Dev Mailingliste über sein Vorhaben Software zu
  veröffentlichen, die Minern erlauben würde ihre Bereitschaft zu signalisieren,
  die Regeln für den in [BIP119][] vorgeschlagenen
  [OP_CHECKTEMPLATEVERIFY][topic op_checktemplateverify] (CTV) Opcode
  durchzusetzen. Sofern in einer der mehreren nächsten 2016-Block
  Schwierigkeitsperioden mindestens 90% der Blöcke ein positives Signal zeigen
  würden, würden Nodes die Rubins Software verwenden die CTV-Regeln ab etwa
  Anfang November durchsetzen.

  Rubin beschrieb ausführlich mehrere Gründe warum er denkt dass Bitcoin-Nutzer
  CTV aktivieren können wollen würden:

  - *Konsistenz*: CTV hat eine stabile Spezifikation und Implementierung

  - *Popularität*: Etliche in der Bitcoin-Sphäre bekannte Personen und Gruppen
    unterstützen CTV

  - *Durchführbarkeit*: Es scheint keine Einwände zu geben die angeben, dass
    CTV gewünschte Eigenschaften von Bitcoin stört

  - *Erwünschtheit*: CTV führt von Nutzern gewünschte Funktionalität ein,
    welche insbesondere [Covenant][topic covenants]-basierte [Vaults][topic
    vaults] ermöglicht

  Rubins Email stieß Beiträge von über einem Dutzend Personen an, die
  teilweise direkt auf seine Email antworteten oder in anderen Threads zur
  Diskussion beitrugen. Wir können nicht alle nennenswerten Antworten
  zusammenfassen, aber es folgen einige Kommentare die wir insbesondere
  erwähnenswert fanden:

  - Anthony Towns [begutachtete][towns ctv signet] die Aktivität auf dem
    CTV [Signet][topic signet]. Fast alle Transaktionen schienen von der
    gleichen Software ([Sapio][]) erzeugt worden zu sein. Dies könnte ein
    Hinweis sein, dass CTV nicht hinreichend von der Öffentlichkeit sondiert
    worden sei. Er merkte außerdem an, dass Änderungen der Konsensregeln zum
    Einführen neuer Funktionalität für alle Bitcoin-Nutzer Risiken erzeugen,
    auch diejenigen welche die neue Funktionalität nicht nutzen wollen. Es sei
    daher wichtig öffentlich darzustellen, dass die neue Funktionalität
    hinreichend wertvoll für deren Nutzer wäre, um die Risiken zu
    rechtfertigen. Dieser Email folgend wurden zusätzliche
    [Experimente][fiatjaf vault] auf dem CTV Signet durchgeführt.

  - Matt Corallo [wand][corallo ctv cost] ebenfalls ein, dass Änderungen der
    Konsensusregeln erhebliche Kosten für alle mit sich bringen und deswegen
    Softforks nur versucht werden sollten sobald klar ist, dass ein
    Änderungsvorschlag den bestmöglichen Nutzen bringt. In Bezug auf Covenants
    würde Corallo sich das "flexibelste, nützlichste und privateste" Design
    wünschen. Er [stellte später fest][corallo not clear], dass er bislang
    nicht überzeugt ist, dass CTV eine wirklich gute Wahl sei.

  - Russell O'Connor [wies][oconnor wizards] im #bitcoin-wizards IRC-Kanal
    darauf hin, dass eine der beschriebenen Anwendungen von CTV mit den bislang
    existierenden Bitcoin-Adressformaten nicht ausgedrückt werden könne. Die
    Nutzung von CTV direkt im scriptPubKey ("bare script") stünde
    Wallet-Entwicklern nur für interne Transaktion oder mithilfe von
    Spezialsoftware welche das Transferieren des Output-Skripts anstelle von
    Adressen übernimmt zur Verfügung. Alternativ könnten Wallets die CTV zum
    Beispiel für [Vaults][topic vaults] verwenden wollen Zahlungen an P2TR
    Adressen empfangen welche dann darauf festgelegt wären später CTV zu
    verwenden.

    Nachdem Towns die Kommentare von O'Connor über die fehlende
    Adressnutzbarkeit auf der Mailingliste [erwähnte][towns bare],
    [antwortete][oconnor bare] O'Connor mit zusätzlichen Details und deutete
    darauf hin, dass das Weglassen der Nutzung von CTV direkt im scriptPubKey
    ein wesentlich einfacheres und komponierbareres Design erlauben würde. Er
    würde jedoch das breiter anwendbare `OP_TXHASH` (siehe [Newsletter
    #185][news185 txhash]) bevorzugen. Rubin [antwortete][rubin bare] mit
    einigen Gegenargumenten.

  - David Harding [beschrieb][harding transitory] Bedenken, dass CTV vielleicht
    keine langfristig Nutzung erfahren würde, da Anwendungsfällen nicht
    materialisieren oder andere Covenant-Konstruktionen populäre Anwendungen
    besser erfüllen können würden. Trotzdem würden Konsensus-Code-Entwickler
    immerwährend der Belastung ausgesetzt, den Code zur Interpretation von CTV
    zu unterstützen und die Interaktionen mit anderen zukünftigen
    Konsensusänderungen zu analysieren. Harding schlug vor CTV nur temporär für
    fünf Jahre zu aktivieren und automatisch danach abzuschalten. Falls CTV
    hinreichend nützlich wäre, könnten dann Bitcoin-Nutzer entscheiden die
    automatische Abschaltung auszusetzen. Keine der Antworten befürwortete
    diesen Vorschlag, wobei die meisten entweder die hohen Kosten oder den
    niedrigen Nutzen kritisierten. Es wurde außerdem angemerkt, dass die
    zukünftige Validierung der Blöcke für die CTV aktiv war trotzdem den
    CTV-Validierungs-Code brauchen würden und somit dieser Code trotz allem
    weiter beibehalten werden müsste.

    Antoine "Darosior" Poinsot [bat][darosior apo] um Feedback zur Idee der
    Aktivierung einer leicht abgeänderten Variante von [BIP118][]
    `SIGHASH_ANYPREVOUT` ([APO][topic sighash_anyprevout]) statt, oder
    zumindest vor, CTV. Die beschriebene Änderung würde APO erlauben die
    Funktionalität von CTV nachzuahmen, würde aber für manche Anwendung höhere
    Kosten bedeuten. Die Aktivierung von APO würde außerdem den ursprünglich
    vorgesehenen Anwendungszweck des [Eltoo][topic eltoo]-Verfahren für das
    Lightning Netzwerk ermöglichen, welches effizientere und sicherere
    Channel-Updates verspricht.

  - James O'Beirne [schlug vor][obeirne benchmark], dass sein CTV-basiertes
    [simple vault][] Design als Maßstab verwendet werden könne um verschiedene
    Covenant-Designs zu bewerten. Sein Design sei insbesondere geeignet, da es
    sehr einfach sei und in seinen Augen deutlich die Sicherheit von vielen
    Bitcoin-Nutzern erhöhen könnte, sofern es einsetzbar würde. Darosior war
    der erste, der diese Herausforderung annahm und den "simple vault" mithilfe
    von `SIGHASH_ANYPREVOUT` [umsetzte][darosior vault].

  Die Diskussion war noch im vollen Gange als diese Zusammenfassung geschrieben
  wurde. Einige andere Unterhaltungen über CTV und Covenant-Technologie
  ergaben sich auf Twitter, IRC, Telegram chats und anderswo. Wir bitten
  Teilnehmer dieser Unterhaltungen wichtige Erkenntnisse an die Bitcoin-Dev
  Mailingliste weiterzutragen.

  Der oben zusammengefassten Diskussion folgend gab Rubin [bekannt][rubin path
  forward], dass er seinen ursprünglichen Plan Aktivierungs-Software
  herauszugeben nicht zeitnah weiterverfolgen würde. Er würde stattdessen das
  erhaltene Feedback auswerten und potenziell später in Zusammenarbeit mit
  anderen CTV-Unterstützern einen überarbeiteten Aktivierungsplan vorstellen.
  Optech wird in zukünftigen Ausgaben des Newsletters weiter über das Thema
  berichten.

## Ausgewählte Fragen und Antworten von Bitcoin Stack Exchange

*[Bitcoin Stack Exchange][bitcoin.se] ist eine der Plattformen auf denen Optech
Mitwirkende zuerst Antworten auf ihre Fragen suchen, oder wenn wir einen
Augenblick Zeit haben, anderen neugierigen oder verwirrten Nutzern aushelfen.
In dieser monatlichen Rubrik stellen wir einige populäre Fragen und Antworten
des letzten Monats vor.*

{% comment %}<!-- https://bitcoin.stackexchange.com/search?tab=votes&q=created%3a1m..%20is%3aanswer -->{% endcomment %}
{% assign bse = "https://bitcoin.stackexchange.com/a/" %}

- [<!--How was the generator point G chosen in the secp256k1 curve?-->Wie wurde der Generatorpunkt G für secp256k1 gewählt?]({{bse}}113116)
  Pieter Wuille beschreibt, dass die Entscheidungsgründe nicht öffentlich
  bekannt sind, aber eine ungewöhnliche Eigenschaft des konstanten
  [Generatorpunktes G][se 29904] auf die Konstruktionsmethode hinweisen
  könnten.

- [<!--What is the maximum, realistic P2P message payload size-->Was ist die maximale realistische Größe der Nutzdaten einer P2P Nachricht?]({{bse}}113059)
  0xb10c fragt ob es eine valide [P2P Nachricht][p2p messages] der größe `MAX_SIZ` (32MB) geben könnte. Pieter Wuille erklärt, dass die `MAX_PROTOCOL_MESSAGE_LENGTH` von ([4MB][bitcoin protocol 4mb] die Größe von eingehenden Nachrichten denial-of-service-Angriffe unterbindet. Die Größe wurde im [Rahmen von segwit][Bitcoin Core #8149] von [2MB][Bitcoin Core #5843] erhöht.

- [<!--Is there evidence for lack of stale blocks?-->Gibt es Belege für die
  geringe Zahl von erloschenen Blöcken (stale blocks)?]({{bse}}113413)
  Lightlike weist auf ein Diagram historischer Blockverbreitungszeitspannen von
  einer Statistikseite des [KIT][KIT statistics] hin und streicht die
  Einführung von [compact block relay][topic compact block relay]
  ([BIP152][]) als einen Grund für das reduzierte Auftreten von erloschenen
  Blöcken hervor. Compact block relay wurde ursprünglich in [#8068][Bitcoin
  Core #8068] implementiert.

  {:.center}
  ![Block Propagation Delay History chart](/img/posts/2022-04-block-propagation-delay.png)

- [Does a coinbase transaction's input field have a VOUT field?]({{bse}}113392)
  Pieter Wuille outlines the requirements for a coinbase transaction's input: the
  prevout hash must be
  `0000000000000000000000000000000000000000000000000000000000000000`, the prevout
  index must be `ffffffff`, it must have a 2-100 byte `scriptSig` length,
  and since [BIP34][] the `scriptSig` must also start with the block height.

## Releases and release candidates

*New releases and release candidates for popular Bitcoin infrastructure
projects.  Please consider upgrading to new releases or helping to test
release candidates.*

- [Bitcoin Core 23.0][] is the next major version of this predominant
  full node software.  The [release notes][bcc23 rn] list multiple
  improvements, including [descriptor][topic descriptors] wallets by
  default for new wallets and descriptor wallets now easily support
  receiving to [bech32m][topic bech32] addresses using [taproot][topic
  taproot].

- [Core Lightning 0.11.0][] is a release of the next major version of
  this popular LN node software.  Among other features and bug fixes,
  the new version supports multiple active channels to the same peer and
  paying [stateless invoices][topic stateless invoices].

- [Rust-Bitcoin 0.28][] is the latest release of this Bitcoin library.
  The most significant change is adding support for taproot and
  improving related APIs, such as those for [PSBTs][topic psbt].  Other
  improvements and bug fixes are described in their [release notes][rb28
  rn].

## Notable code and documentation changes

*Notable changes this week in [Bitcoin Core][bitcoin core repo], [Core
Lightning][core lightning repo], [Eclair][eclair repo], [LDK][ldk repo],
[LND][lnd repo], [libsecp256k1][libsecp256k1 repo], [Hardware Wallet
Interface (HWI)][hwi repo], [Rust Bitcoin][rust bitcoin repo], [BTCPay
Server][btcpay server repo], [BDK][bdk repo], [Bitcoin Improvement
Proposals (BIPs)][bips repo], and [Lightning BOLTs][bolts repo].*

- [LND #5157][] adds an `--addpeer` startup option which opens a peer
  connection with the provided node.

- [LND #6414][] begins advertising support for keysend [spontaneous
  payments][topic spontaneous payments] when enabled.  LND has supported
  keysend since 2019 but it was initially deployed with no way for nodes
  to advertise they supported it.  Implementations of keysend in other
  LN node software announced support in their node advertisements and
  this merged PR for LND replicates that.

{% include references.md %}
{% include linkers/issues.md v=2 issues="5157,6414,5843,8149,8068" %}
[bitcoin core 23.0]: https://bitcoincore.org/bin/bitcoin-core-23.0/
[bcc23 rn]: https://github.com/bitcoin/bitcoin/blob/master/doc/release-notes/release-notes-23.0.md
[core lightning 0.11.0]: https://github.com/ElementsProject/lightning/releases/tag/v0.11.0.1
[rust-bitcoin 0.28]: https://github.com/rust-bitcoin/rust-bitcoin/releases/tag/0.28.0
[rb28 rn]: https://github.com/rust-bitcoin/rust-bitcoin/blob/master/CHANGELOG.md#028---2022-04-20-the-taproot-release
[rubin ctv-st]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020233.html
[towns ctv signet]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020234.html
[sapio]: https://learn.sapio-lang.org/
[corallo ctv cost]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020263.html
[corallo not clear]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020289.html
[oconnor wizards]: https://gnusha.org/bitcoin-wizards/2022-04-19.log
[towns bare]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020245.html
[oconnor bare]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020256.html
[rubin bare]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020260.html
[harding transitory]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020242.html
[darosior apo]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020276.html
[obeirne benchmark]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2022-April/020280.html
[simple vault]: https://github.com/jamesob/simple-ctv-vault
[news185 txhash]: /en/newsletters/2022/02/02/#composable-alternatives-to-ctv-and-apo
[fiatjaf vault]: https://twitter.com/fiatjaf/status/1517836181240782850
[rubin path forward]: https://twitter.com/JeremyRubin/status/1518477022439247872
[darosior vault]: https://twitter.com/darosior/status/1518961471702642689
[se 29904]: https://bitcoin.stackexchange.com/questions/29904/
[p2p messages]: https://developer.bitcoin.org/reference/p2p_networking.html#data-messages
[bitcoin protocol 4mb]: https://github.com/bitcoin/bitcoin/commit/2b1f6f9ccf36f1e0a2c9d99154e1642f796d7c2b
[KIT statistics]: https://www.dsn.kastel.kit.edu/bitcoin/index.html
[se 5866]: https://bitcoin.stackexchange.com/a/5866/87121
