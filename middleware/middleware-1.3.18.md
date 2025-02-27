---
slug: /release-notes/middleware/1.3.18
title: Version 1.3.18
---

# fiskaltrust.Middleware 1.3.18 (Germany)
_March 26, 2021_

This Middleware version contains bug fixes for issues that could occur when using _Swissbit Cloud_ or _Deutsche Fiskal_ TSEs behind a proxy server, a stability improvement for our _CryptoVision_ SCU to handle Windows' USB power saving mode, and a minor improvement for generating TSE order payloads.

<div class="alert alert--warning" role="alert">Version 1.3 of the Middleware is meant for the German market only, customers in Austria and France should continue to use version 1.2. We will unify these experiences in an upcoming version.</div>

## Stability improvement: Improved USB power-saving mode handling for CryptoVision TSEs
Some of our partners reported very infrequent errors with CryptoVision TSEs, with the logs hinting that the TSE was not responding with an appropriate data header. After clarifications with the vendor, this might be related to the TSE being switched into USB power saving mode by Windows - which of course increases the response time. 

The typical error message for this problem is: `Response headers didn´t match. Expected <QWRWYW5jRUQgU2VDdVJlIFNEL01NQyBDQXJkAQ==>, but instead got <AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA==>.`

According to our logs, this issue occurred very rarely, but some systems were disproportionately affected. This might be dependent on the OS or system configuration.

While the Middleware is usually able to handle one-time TSE errors with it's inbuilt retry mechanisms, this issue is particularly critical, as the TSE actually processes the transmitted data. However, since the Middleware ist not able to read the response, this leads to different consequential errors, depending on the failing operation. 

In the worst case, a _finish transaction_ request was affected, which could block daily-closing receipts in some cases (as the transaction is marked open in our database, but closed on the TSE). We've added an additional [_ftReceiptCaseFlag_](https://docs.fiskaltrust.cloud/docs/poscreators/middleware-doc/germany/reference-tables/ftreceiptcase#ftreceiptcaseflag) (_0x0000000020000000_) to the daily-closing receipt that can be used to resolve this situation.

<div class="alert alert--warning" role="alert">As neither we nor our partners are able to reliably reproduce this issue, we have released our potential fix as a release candidate package. This fix might change according to the feedback we receive.</div>

## Bug fix: Configured proxy server is not used for downloading Fiskal Cloud Connector
When running the Middleware behind a proxy server and using the Middleware's automatic FCC download capabilities, the proxy parameters that were specified for `fiskaltrust.exe` were ignored in some cases, rendering it impossible to automatically download and setup the FCC. This issue (which affected both the Swissbit Cloud and the Deutsche Fiskal SCUs) has been resolved.

## Bug fix: TSE process data was not computed correctly for some rare info-order cases
The process data of _info-order_ receipts (which end up as _Bestellung-V1_ on TSEs) was not computed correctly in some rare cases; namely, when a `ChargeItem.Amount` of `0.00` was sent, the Middleware sent this as `.00` to the TSE (instead of `0.00`). This issue has been resolved.


## Affected packages
Packages not listed here were not updated, as we decided to not increase the version of unchanged packages. All packages with versions greater or equal to 1.3.1 are compatible with each other (it is e.g. possible to use _fiskaltrust.Middleware.SCU.Swissbit.1.3.1_ with the new queue packages).

- _fiskaltrust.Middleware.Queue.EF v1.3.18_
- _fiskaltrust.Middleware.Queue.MySQL v1.3.18_
- _fiskaltrust.Middleware.Queue.SQLite v1.3.18_
- _fiskaltrust.Middleware.SCU.DE.SwissbitCloud v1.3.18_
- _fiskaltrust.Middleware.SCU.DE.DeutscheFiskal v1.3.18_
- _fiskaltrust.Middleware.SCU.DE.CryptoVision v1.3.18-rc1_

## Next steps in the Middleware
We will continue to improve the stability of our Middleware in the next sprints. As always, we're happy to hear feedback and suggestions via [feedback+middleware@fiskaltrust.cloud](mailto:feedback+middleware@fiskaltrust.cloud) or directly via issues in our GitHub repositories.
