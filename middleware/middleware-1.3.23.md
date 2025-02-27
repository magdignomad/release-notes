---
slug: /release-notes/middleware/1.3.23
title: Version 1.3.23
---

# fiskaltrust.Middleware 1.3.23 (Germany)
_September 15, 2021_

In this version of the Middleware, we added the possibility to easily identify mandatory and optional signature items when printing receipts. Additionally, the Middleware is now able to automatically update the _Fiskal Cloud Connector_, and several bugs and stability issues have been resolved. This release also marks the first non-RC version of the _FiskalyCertified_ SCU (which added support for fiskaly's certified version 2).


:::caution

Version 1.3 of the Middleware is meant for the German market only, customers in Austria and France should continue to use version 1.2. We will unify these experiences in an upcoming version.

:::

## Feature: Flag optional signature items
Starting with this version, we're adding the _ftSignatureFormatFlag_ `0x0000000000010000` to signatures that are not mandatory to print. Especially with the recently changed _KassenSichV_ regulations in Germany (which allow to omit printing most texts when a QR code is added), this should enable PosCreators to more easily decide which information needs to be printed onto the receipt and which information is optional.

More information can be found in the [respective docs](https://docs.fiskaltrust.cloud/docs/poscreators/middleware-doc/germany/reference-tables/ftsignatureformat). 

:::tip 

Please note that this behavior can be deactivated by setting the Queue configuration parameter `FlagOptionalSignatures` to `false`.

:::

## Feature: Automatically update FCC with SCU update
Starting from this version, each _SwissbitCloud_ and _DeutscheFiskal_ SCU version is tied to a specific minimal Fiskal Cloud Connector (FCC) version. If the currently installed FCC version is lower than this, the Middleware will automatically download and update the newer FCC. This should enable users that have already installed previous versions to profit from the latest improvements in this external dependency.

## Feature: Make HTTP timeout and proxy settings configurable in FiskalyCertified SCU
We've made the HTTP timeout and the used HTTP proxy configurable when using the _FiskalyCertified_ SCU for fiskaly's v2 TSEs. The following parameters have been added:
- `FiskalyClientTimeout` (in milliseconds, default: `30000`)
- `ProxyServer`
- `ProxyPort`
- `ProxyUsername` (leave empty for anonymous access)
- `ProxyPassword` (leave empty for anonymous access)

## Feature: Support automatically removing already closed transactions in zero-receipts
The _ftReceiptCaseFlag_ `0x0000000020000000`, which removes open transactions from our database that are already marked as closed on the TSE, is now also supported in zero-receipts (previously, this was only the case for daily-closing receipts). This should enable Middleware users to escape rare cases in which e.g. the TSE's answer got lost, but the request to the device or service was actually processed.

## Feature: SwissbitCloud support in _Bring your own Data Center_
Our _Bring your own Data Center_ solution now supports _SwissbitCloud_ and _DeutscheFiskal_ TSEs using self hosted FCCs. To make use of this, the Fiskal Cloud Connector should be installed on a machine or container in the same network, and the SCU configured to use it via the `FccUri` configuration parameter (more details in our [docs](https://docs.fiskaltrust.cloud/docs/poscreators/middleware-doc/germany/scu/swissbit-cloud)).

## Bug fix: Bring your own Data Center didn't work with custom Redis instance
We've fixed an issue where the connection string to custom Redis instances would be assembled wrongly.

## Bug fixes: Small DSFinV-K improvements
We've fixed three issues in our DSFinV-K exports that could occur in some edge cases: 
- `UMS_BRUTTO` was calculated wrongly when discounts were sent in a specific format
- `REF_BON_ID` was incorrectly set to the value of `cbReceiptReference` instead of `ftReceiptIdentification` (only applied to local exports, Portal exports were not affected)
- Some timestamps in _transactions.csv_ were not written in UTC, but in the local timezone (only applied to local exports, Portal exports were not affected)

## Bug fixes: FiskalyCertified SCU returned an empty certification ID
We've fixed an issue where the new _FiskalyCertified_ SCU wrongly returned an empty certification ID instead of the correct value `BSI-K-TR-0403-2021`.

## Documentation: Process documentation for DSFinV-K exports
We have created documentation templates for the mandatory process documentation (_Verfahrensdokumentation_), in which we describe how the Middleware populates specific fields in the DSFinV-K export, and how exports can be downloaded from the Portal if the users bought a PosArchive package. This template can be added to the PosCreator's or PosDealer's process documentation and might be useful in case questions occur during audits.

It's available in the [German Portal's download section](https://portal.fiskaltrust.de/AccountProfile/Download).

## Affected packages
Packages not listed here were not updated, as we decided to not increase the version of unchanged packages. All packages with versions greater or equal to 1.3.1 are compatible with each other (it is e.g. possible to use _fiskaltrust.Middleware.SCU.Swissbit.1.3.1_ with the new queue packages).

- _fiskaltrust.Middleware.Queue.EF v1.3.23_
- _fiskaltrust.Middleware.Queue.MySQL v1.3.23_
- _fiskaltrust.Middleware.Queue.SQLite v1.3.23_
- _fiskaltrust.Middleware.SCU.DE.FiskalyCertified v1.3.23_
- _fiskaltrust.Middleware.SCU.DE.SwissbitCloud v1.3.23_
- _fiskaltrust.Middleware.SCU.DE.DeutscheFiskal v1.3.23_

## Next steps in the Middleware
We will focus on further increasing the Middleware's stability and performance in the upcoming sprints. As always, we're happy to hear feedback and suggestions via [feedback+middleware@fiskaltrust.cloud](mailto:feedback+middleware@fiskaltrust.cloud) or directly via issues in our GitHub repositories.
