---
title: 'Bitcoin Optech Newsletter #153'
permalink: /en/newsletters/2021/06/16/
name: 2021-06-16-newsletter
slug: 2021-06-16-newsletter
type: newsletter
layout: newsletter
lang: en
---
This week's newsletter celebrates the lock in of the taproot soft fork,
describes a draft BIP for improving transaction
privacy by varying the fields used to implement anti fee sniping, and
features an article about the challenges of mixing transaction
replacement with payment batching.  Also included are our regular
sections with announcements of new software releases and release
candidates, plus notable changes to popular Bitcoin infrastructure
software.

## News

- **🟩  Taproot locked in:** the [taproot][topic taproot] soft fork and
  related changes specified in BIPs [340][bip340], [341][bip341], and
  [342][bip342] were locked in by signaling miners last weekend.
  Taproot will be safe to use after block 709,632, which is expected in
  early or mid November.  The delay gives time for users to upgrade
  their nodes to a release (such as Bitcoin Core 0.21.1) that will
  enforce taproot's rules, ensuring that funds received to taproot
  scripts after block 709,632 are safe even if there's a problem with
  miners.

    Developers are encouraged to start [implementing taproot][taproot
    uses] in their software during the next several months so they can
    be ready to take advantage of greater efficiency, privacy, and
    fungibility as soon as the activation is complete.

    Readers celebrating the lock in of taproot may also wish to read a
    [short history][wuille taproot] of taproot's origins and history by
    developer Pieter Wuille.

- **BIP proposed for wallets to set nSequence by default on taproot transactions:**
  Chris Belcher [posted][belcher post] a draft BIP to the Bitcoin-Dev
  mailing list suggesting an alternative way wallets can implement anti
  fee sniping that would enhance the privacy and fungibility of
  transactions made by single-sig users, [multisignature][topic
  multisignature] users, and users of certain contract protocols such as
  taproot-enabled LN or advanced [coinswaps][topic coinswap].

    Fee sniping is a problem that may occur as Bitcoin's subsidy
    continues to diminish and transaction fees begin to dominate
    Bitcoin's block rewards.  If transaction fees are all that matter,
    then a miner with `x` percent of the hash rate has a `x` percent
    chance of mining the next block, so the expected value to them of
    honestly mining is `x` percent of the [best feerate set of
    transactions][csb] in their mempool.

    Alternatively, a miner could dishonestly attempt to re-mine the
    immediately previous block plus a wholly new block to extend the
    chain.  That's fee sniping and their chance of succeeding at it if
    every other miner is honest[^worse] is `(x/(1-x))^2`.  Even though
    fee sniping has a overall lower probability of success than honest
    mining, attempting dishonest mining could be the more profitable
    choice if transactions in the previous block paid significantly
    higher feerates than the transactions currently in the mempool---a
    small chance at a large amount can be worth more than a large chance
    at a small amount.

    If a dishonest miner fee snipes a previous block, they don't need to
    use the exact same set of transactions previously included in that
    block.  They can replace any low feerate transactions in that block
    with higher feerate transactions from their mempool.  This has the
    benefit of burying higher feerate transactions further back in the
    chain where other dishonest miners will have to work harder to
    re-mine them a second time.

    {:.center}
    ![Illustration of honest mining compared to fee sniping](/img/posts/2021-06-afs.png)

    Anti fee sniping mitigates this to a certain degree.  When used on
    high feerate transactions, it prevents miners from being able to
    include them in the re-mined version of any previous block.  In
    other words, in an ideal situation, anti fee sniping would prevent
    there from being any difference between the regular transactions in
    blocks created by economically rational dishonest miners and blocks
    created by honest miners.  This can reduce the revenue available to
    the dishonest miner and make them more vulnerable to fee sniping by
    other dishonest miners---reducing the initial dishonest miner's
    expected rewards possibly enough to keep them mining honestly in the
    first place.  We're unaware of any developers who think this type of
    anti fee sniping is a complete solution to fee-only incentive
    problems, but it has the advantage over most other mitigations of
    not requiring any consensus changes.  Originally
    [implemented][bitcoin core #2340] in Bitcoin Core, anti fee sniping
    is also used today by Electrum, Blockstream Green, LND (see
    [Newsletter #18][news18 lnd afs]), and C-Lightning (see [Newsletter
    #84][news84 cl afs]).

    All wallets that implement anti fee sniping today use nLockTime
    height locks to prevent a transaction from being included in the
    re-mined version of a previous block.  It's also possible to
    implement the same protection using [BIP68][] nSequence height
    locks.  This wouldn't be any more effective at preventing fee
    sniping, but it would provide a good reason for regular wallets to
    set their nSequence values to the same values that are required for
    transactions in certain [multisignature][topic multisignature]-based
    contract protocols, such as ideas for coinswaps and taproot-enabled
    LN.  This helps make regular wallet transactions look like contract
    protocol transactions and vice versa.

    In addition to using nSequence, some contract protocols may also use
    nLockTime, so Belcher's proposal suggests wallets randomly choose
    between using either nLockTime or nSequence with 50% probability
    when both options are available.  Overall, if the proposal is
    implemented, it will allow users of regular single-sig transactions
    or uncomplicated multisignatures to join together with users of
    contract protocols to mutually improve each others' privacy and
    fungibility.

## FIXME:article

FIXME:harding to rebase on #582 on Tuesday

## Releases and release candidates

*New releases and release candidates for popular Bitcoin infrastructure
projects.  Please consider upgrading to new releases or helping to test
release candidates.*

- FIXME:harding Rust-Bitcoin 0.26.1 and .2

- FIXME:harding Rust-Bitcoin 0.0.98

- [LND 0.13.0-beta.rc5][LND 0.13.0-beta] is a release candidate that
  adds support for using a pruned Bitcoin full node, allows receiving
  and sending payments using Atomic MultiPath ([AMP][topic multipath payments]),
  and increases its [PSBT][topic psbt] capabilities, among other improvements
  and bug fixes.

## Notable code and documentation changes

*Notable changes this week in [Bitcoin Core][bitcoin core repo],
[C-Lightning][c-lightning repo], [Eclair][eclair repo], [LND][lnd repo],
[Rust-Lightning][rust-lightning repo], [libsecp256k1][libsecp256k1
repo], [Hardware Wallet Interface (HWI)][hwi repo],
[Rust Bitcoin][rust bitcoin repo], [BTCPay Server][btcpay server repo],
[Bitcoin Improvement Proposals (BIPs)][bips repo], and [Lightning
BOLTs][bolts repo].*

- [Bitcoin Core GUI #4][] UI external signer support (e.g. hardware wallet) FIXME:dongcarl

- [Bitcoin Core #21573][] updates the version of libsecp256k1 included
  in Bitcoin Core.  Of the changes, the most notable in the use of the
  optimized modular inverse code described in Newsletters [#136][news136
  safegcd] and [#146][news146 safegcd].  Performance evaluations posted
  to the PR found it to speed up old block verification by about 10%.

- [C-Lightning #4591][] bech32m support FIXME: Xekyo

## Footnotes

[^worse]:
    The problem is actually worse than described because every miner
    who chooses to mine dishonestly reduces the number of honest
    miners trying to extended the chain.  The smaller the share of hash
    rate controlled by honest miners, the greater the probability that a
    dishonest miner will be successful, so a single large miner
    rationally choosing to mine dishonestly can set off a cascade of
    ever smaller miners also rationally defecting to dishonest mining.
    If that persists for an extended period of time, confirmation scores
    cease to be a proxy for transaction finality and Bitcoin becomes
    unusable until the problem is resolved.  We expect the most likely
    resolution would be centralization of mining---a cartel representing
    a majority of hashrate agreeing to never reorg each others blocks
    can restore stability to the system, but that comes with the
    increased risk that they'll later choose to (or be pressured into)
    censoring certain transactions.

{% include references.md %}
{% include linkers/issues.md issues="4,21573,4591,2340" %}
[LND 0.13.0-beta]: https://github.com/lightningnetwork/lnd/releases/tag/v0.13.0-beta.rc5
[belcher post]: https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2021-June/019048.html
[csb]: /en/newsletters/2021/06/02/#candidate-set-based-csb-block-template-construction
[news18 lnd afs]: /en/newsletters/2018/10/23/#lnd-1978
[news84 cl afs]: /en/newsletters/2020/02/12/#c-lightning-3465
[news136 safegcd]: /en/newsletters/2021/02/17/#faster-signature-operations
[news146 safegcd]: /en/newsletters/2021/04/28/#libsecp256k1-906
[taproot uses]: https://en.bitcoin.it/wiki/Taproot_Uses
[wuille taproot]: https://twitter.com/pwuille/status/1403725170993336322
