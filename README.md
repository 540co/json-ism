# JSON Information Security Marking Standard (JSON-ISM)

## Introduction

Department of Defense (DoD) policy requires the identification and protection of national security information and controlled unclassified information (CUI).  Department of Defense Manual (DoDM) 5200.01, Volumes 2 and 4 (referenced below) describe how to appropriately mark classified information and CUI to facilitate information sharing.  These markings are used (along with other factors) to make access/dissemination decisions.

Extensible Markup Language (XML) is widely used within the DoD to share information and a comprehensive marking standard called Information Security Marking Metadata (ISM or IC-ISM) has been made available by the Office of the Director of National Intelligence (referenced below).  We are not aware of any similar marking standards based on JavaScript Object Notation (JSON).  With the ever increasing popularity of REST APIs for sharing information between applications/systems, many now using JSON over XML, a standard for marking JSON documents has become necessary.  The JSON Information Security Marking Standard (JSON-ISM) aims to be that standard.

## References

* [DoDI 5200.48 - Controlled Unclassified Information (CUI)](https://www.esd.whs.mil/Portals/54/Documents/DD/issuances/dodi/520048p.PDF?ver=2020-03-06-100640-800)
* [DoDI 5230.24 - Distribution Statements on Technical Documents](https://www.darpa.mil/attachments/Distribution%20Statements%20on%20Technical%20Documents-%20updated.pdf)
* [Office of the Director of National Intelligence - Information Security Marking Metadata (ISM)](https://www.dni.gov/index.php/about/organization/chief-information-officer/information-security-marking-metadata)

## Overview

JSON-ISM facilitates marking information based upon the concept of Banner Lines (Resource Level) and Portion Markings (Portion Level) as described in the DoDM 5200.01 volumes.  JSON-ISM provides the attributes necessary for consumers to construct Banner Lines and Portion Markings to be used when displaying the information.  In addition, these attributes can be used when making access/dissemination decisions.  Where possible, JSON-ISM attempts to align with the IC-ISM standard.

JSON-ISM is only a marking standard.  It remains the responsibility of the data owner(s) to ensure information is appropriately marked and those in possession of classified information or CUI are responsible for its protection.

## Distribution Notice :warning:

All data contained within this repo (including this file) is `UNCLASSIFIED`.

> Classification markings are for illustration purposes only.

## ISM Object

ISM attributes will be grouped together to form an object.  This object can be used at both the resource level (overall document) and the portion level (individual attribute).  

The object is identified at the resource level using the `ism` key.  This key is placed at the root level of the JSON document.

**Marking at the resource level (equivalent to Banner Line for overall document classification)**
```json
{
  "ism":{
    "version": "1",
    "classification": "U",
    "ownerProducer": [
      "USA"
    ]
  },
  "data":{
    ...
  }
}
```

The object is identified at the portion level using the attribute name plus the `Ism` key suffix.  For example, if the key for the portion we wish to mark is `description`, the key for the ISM object would be `descriptionIsm`.

**Marking at the portion level (equivalent to Portion Marking for identifying portions within a document)**
```json
{
  ...
  "data":{
    "programName": "Nonsensitive Program Name",
    "description": "Sensitive Description",
    "descriptionIsm": {
      "classification": "U",
      "ownerProducer": [
        "USA"
      ],
      "disseminationControls": [
        "FOUO"
      ]
    },
    ...
  }
}
```

## ISM Based on Classification

This sections describes which attributes of the ism object are required based on the sensitivity of the information.

### Unclassified Information

When dealing with unclassified information that has no limitations on dissemination, all ISM attributes are optional.  It is recommended however to include `version`, `classification`, and `ownerProducer` at the resource level.

### Classified Information

When dealing with classified information, the `classification` and `ownerProducer` attributes are required.  All other attributes are optional.

## Attributes

The following is a list of ISM object attributes.  These attributes provide the information necessary to construct Banner Lines and Portion Markings.

### JSON-ISM Version

The `version` attribute (String) identifies the version of JSON-SMS being used (for the entire document).  This attribute is only used at the resource level.

### Classification

The `classification` attribute (String) is used to identify the highest classification of information included within a document (Banner Line - Resource Level) or within a given portion of a document (Portion Marking - Portion Level).  This attribute is always used in conjunction with the `ownerProducer` attribute.  Taken together, these two attributes specify the classification category and type of classification (US, non-US, or Joint).

The possible values for `classification` are:

| Value | Description |
| ----- | ----------- |
| R | RESTRICTED |
| C | CONFIDENTIAL |
| S | SECRET |
| TS | TOP SECRET |
| U | UNCLASSIFIED |

**Example**
```json
  "classification": "U"
```

### Controlled By Name

The `controlledByName` attribute (String) specifies the name of the DoD component determining that the information is CUI for the resource
**Example**
```json
  "controlledByName": "Department of the Navy"
```

### Controlled By Office

The `controlledByOffice` attribute (String) is the DoD office determining that the information is CUI for the resource
**Example**
```json
  "controlledByOffice": "XYZ-456"
```

### Owner Producer

The `ownerProducer` attribute (Array[String]) is used to identify one or more national governments or international organizations that have purview over the classification marking of a resource or portion therein. This attribute is always used in conjunction with the `classification` attribute.  Taken together, these two attributes specify the classification category and type of classification (US, non-US, or Joint).

**Example**
```json
  "ownerProducer": [
    "USA"
  ]
```
### Point Of Contact
The `poc` attribute attribute (String) contains the phone number or email address for the originating DoD Component or authorized CUI holder for the resource.

**Example**
```json
  "poc": "+1 (202) 555-4545"
```

### Joint

The `joint` attribute (Boolean), when true, is used to signify that multiple values in the `ownerProducer` attribute are JOINT owners of the data.

**Example**
```json
  "joint": true
```

### SCI Controls

The `sciControls` attribute (Array[String]) identifies one or more sensitive compartmented information control systems.

The possible patterns for `sciControls` are:

| Pattern | description |
| ------- | ----------- |
| KDK-BLFH-[A-Z0-9]{1,6} | KDK-BLFH-xxxxxx, xxxxxx represents up to 6 alphanumeric characters indicating a sub BLUEFISH compartment |
| KDK-IDIT-[A-Z0-9]{1,6} | KDK-IDIT-xxxxxx, xxxxxx represents up to 6 alphanumeric characters indicating a sub IDITAROD compartment |
| KDK-KAND-[A-Z0-9]{1,6} | KDK-KAND-xxxxxx, xxxxxx represents up to 6 alphanumeric characters indicating a sub KANDIK compartment |
| RSV-[A-Z0-9]{3} | RSV-XXX, XXX represents 3 alpha numeric characters to indicate sub Reserve compartments |
| SI-G-[A-Z]{4} | G-AAAA, AAAA represents 4 alpha characters to indicate sub Gamma compartments |
| SI-[A-Z]{3} | SPECIAL INTELLIGENCE compartment |
| SI-[A-Z]{3}-[A-Z]{4} | SPECIAL INTELLIGENCE sub-compartment |

The possible values for `sciControls` are:

| Value | Description |
| ----- | ----------- |
| EL | ENDSEAL |
| EL-EU | ECRU |
| EL-NK | NONBOOK |
| HCS | HCS |
| HCS-O | HCS-O |
| HCS-P | HCS-P |
| KDK | KLONDIKE |
| KDK-BLFH | KDK BLUEFISH |
| KDK-IDIT | KDK IDITAROD |
| KDK-KAND | KDK KANDIK |
| RSV | RESERVE |
| SI | SPECIAL INTELLIGENCE |
| SI-G | SI-GAMMA |
| TK | TALENT KEYHOLE |

**Example**
```json
  "sciControls": [
    "RSV-540"
  ]
```

### SAR Identifiers

The `sarIdentifiers` attribute (Array[String]) identifies one or more defense or intelligence programs for which special access is required.

The possible patterns for `sarIdentifiers` are:

| Pattern | Description |
| ------- | ----------- |
| [A-Z\s0-9\-]{1,100} | SPECIAL ACCESS REQUIRED-XXX, the Digraph or Trigraph of the SAR is represented by the XXX |
| [A-Z]{2,} | SPECIAL ACCESS REQUIRED-XXX, the Digraph or Trigraph of the SAR is represented by the XXX |
| [A-Z]{2,}-[A-Z][A-Z0-9]+ | SPECIAL ACCESS REQUIRED-XXX, the Digraph or Trigraph of the SAR is represented by the XXX |
| [A-Z]{2,}-[A-Z][A-Z0-9]+-[A-Z0-9]{2,} | SPECIAL ACCESS REQUIRED-XXX, the Digraph or Trigraph of the SAR is represented by the XXX |

### Atomic Energy Markings

The `atomicEnergyMarkings` attribute (Array[String]) identifies one or more Department of Energy (DoE) markings.

The possible patterns for `atomicEnergyMarkings` are:

| Pattern | Description |
| ------- | ----------- |
| RD-SG-((14)&#124;(15)&#124;(18)&#124;(20)) | RD-SIGMA-#, # represents the SIGMA number which may be 14, 15, 18, or 20 |
| FRD-SG-((14)&#124;(15)&#124;(18)&#124;(20)) | FRD-SIGMA-#, # represents the SIGMA number which may be 14, 15, 18, or 20 |

The possible values for `atomicEnergyMarkings` are:

| Value | Description |
| ----- | ----------- |
| RD | RESTRICTED DATA |
| RD-CNWDI | RD-CRITICAL NUCLEAR WEAPON DESIGN INFORMATION |
| FRD | FORMERLY RESTRICTED DATA |
| DCNI | DoD CONTROLLED NUCLEAR INFORMATION |
| UCNI | DoE CONTROLLED NUCLEAR INFORMATION |
| TFNI | TRANSCLASSIFIED FOREIGN NUCLEAR INFORMATION |

### Dissemination Controls

The `disseminationControls` attribute (Array[String]) identifies one or more indicators for expanding or limiting the distribution of information.

The possible values for `disseminationControls` are:

| Value | Description |
| ----- | ----------- |
| RS | RISK SENSITIVE |
| OC | ORIGINATOR CONTROLLED |
| OC-USGOV | ORIGINATOR CONTROLLED US GOVERNMENT |
| IMC | CONTROLLED IMAGERY |
| NOFORN | NOT RELEASABLE TO FOREIGN NATIONALS |
| PR | CAUTION-PROPRIETARY INFORMATION INVOLVED |
| REL | AUTHORIZED FOR RELEASE TO |
| RELIDO | RELEASABLE BY INFORMATION DISCLOSURE OFFICIAL |
| EYES | EYES ONLY |
| DSEN | DEA SENSITIVE |
| FISA | FOREIGN INTELLIGENCE SURVEILLANCE ACT |
| DISPLAY ONLY | DISPLAY ONLY (AUTHORIZED FOR DISPLAY BUT NOT RELEASE TO) |
| FED ONLY | FEDERAL EMPLOYEES ONLY |
| FEDCON | FEDERAL EMPLOYEES AND CONTRACTORS ONLY |
| NOCON | NO DISSEMINATION TO CONTRACTORS |
| DL ONLY | DISSEMINATION LIST CONTROLLED |
| REL TO USA, LIST | AUTHORIZED FOR RELEASE TO CERTAIN FOREIGN NATIONALS ONLY |

### Distribution Statement
The `distributionStatement` attribute (String) identifies legacy distribution statements for technical documents, including research, development, engineering, test, sustainment, and logistics information and serve the purpose to denote the extent to which they are available for secondary distribution, release, and dissementation without additional approvals or authorizations (pursuant to [DoDI 5230.24](https://www.darpa.mil/attachments/Distribution%20Statements%20on%20Technical%20Documents-%20updated.pdf)).  

Distribution Statements must not be altered in any way from policy and as documented below. Distribution Statement A is only valid for UNCLASSIFIED data.  Distribution Statements B, C, D, E, and F may be used with UNCLASSIFIED or CLASSIFIED data.  Distribution Statements B, C, D, and E are expressed in a standard format and should not only include the Authorized audience, but the Reason for control, Data of determination, and controlling office (identified as variables in parentheses in the distribution statement).

List of possible values for `distributionStatement`:

| Distribution Statements (*with Fill Ins*) |
| ----------------------------------------------- |
| DISTRIBUTION STATEMENT A. Approved for public release. |
| DISTRIBUTION STATEMENT B. Distribution authorized to U.S. Government agencies only *(fill in reason) (date of determination)*. Other requests for this document shall be referred to *(insert controlling DoD office)*. |
| DISTRIBUTION STATEMENT C. Distribution authorized to U.S. Government agencies and their contractors *(fill in reason) (date of determination)*. Other requests for this document shall be referred to *(insert controlling DoD office)*. |
| DISTRIBUTION STATEMENT D. Distribution authorized to the Department of Defense and U.S. DoD contractors only *(fill in reason) (date of determination)*. Other requests shall be referred to *(insert controlling DoD office)*. |
| DISTRIBUTION STATEMENT E. Distribution authorized to DoD Components only *(fill in reason) (date of determination)*. Other requests shall be referred to *(insert controlling DoD office)*. |
| DISTRIBUTION STATEMENT F. Further dissemination only as directed by *(inserting controlling DoD office)* *(date of determination)* or higher DoD authority. |

### Third Party-Imposed Distribution Statements
The `3rdPartyDistributionStatement`,  `3rdPartyDistributionWarning`, and `3rdPartyDistributionContract` support additional distribution statements (pursuant to [DoDI 5230.24](https://www.darpa.mil/attachments/Distribution%20Statements%20on%20Technical%20Documents-%20updated.pdf) Enclosure 5) which generally allow contractors to retain ownership of the intellectual property that is embodied in technical data, documents, or information that is delivered or otherwise provided to the Government.  If a third party-imposed distribution statement is included in the ISM, the `3rdPartyDistributionWarning` and `3rdPartyDistributionContract`are both required as well.

Typically, the `3rdPartyDistributionStatement` can be defined by five pre-defined distribution statements in the table below; however, in the event that an informal pre-existing marking was authorized under a previous Government contract, that authorized marking should be included as the value for `3rdPartyDistributionStatement`.

| Third Party-Imposed Distribution Statements |
| ------------------------------------------- |
| The Government Purpose Rights |
| Limited Rights |
| Restricted Rights |
| Special License Rights |
| Small Business Innovation Research (SBIR) Program |

When included, the value of `3rdPartyDistributionWarning` should always be as follows:

```
The Government's rights to use, modify, reproduce, release, perform, display, or disclose these technical data are restricted by paragraph (b)(2) of the Rights in Technical Data-Noncommercial Items clause contained in the above identified contract.  No restrictions apply after the expiration date shown above.  Any reproduction of technical data or portions thereof marked with this legend must also reproduce markings.
```

When included, the `3rdPartyDistributionContract` is a JSON Object containing four required fields: `contractNumber`, `contractorName`, `contractorAddress`, and `expirationDate`.  See below example:

```json
  "3rdPartyDistributionContract": {
    "contractNumber": "",
    "contractorName": "",
    "contractorAddress": "",
    "expirationDate": ""
  }
```

### Copyright
The `copyright` value may be added as a Third Party-Imposed Distribution Statement (pursuant to [DoDI 5230.24](https://www.darpa.mil/attachments/Distribution%20Statements%20on%20Technical%20Documents-%20updated.pdf) Enclosure 5). The specificied format of a `copyright` value is: `Copyright` or `©` followed by [Date] [Copyright Owner].


### Display Only To

The `displayOnlyTo` attribute (Array[String]) identifies one or more countries and/or international organizations to which classified information may be displayed but NOT released based on the determination of an originator in accordance with established foreign disclosure procedures. This attribute is used in conjunction with the `DISPLAYONLY` value of the `disseminationControls` attribute.

The possible patterns for `displayOnlyTo` are:

| Pattern | description |
| ------- | ----------- |
| NATO/[a-zA-Z\-_] | North Atlantic Treaty Organization Special Words |

The possible values for `displayOnlyTo` are:

| Value | Description |
| ----- | ----------- |
| USA | United States |
| ABW | Aruba |
| AFG | Islamic Republic of Afghanistan |
| AGO | Republic of Angola |
| AIA | Anguilla |
| ALB | Republic of Albania |
| AND | Principality of Andorra |
| ARE | United Arab Emirates |
| ARG | Argentine Republic |
| ARM | Republic of Armenia |
| ASM | Territory of American Samoa |
| ATA | Antarctica |
| ATF | French Southern and Antarctic Lands |
| ATG | Antigua and Barbuda |
| AUS | Commonwealth of Australia |
| AUT | Republic of Austria |
| AX2 | Guantanamo Bay Naval Base |
| AZE | Republic of Azerbaijan |
| BDI | Republic of Burundi |
| BEL | Kingdom of Belgium |
| BEN | Republic of Benin |
| BES | Bonaire, Sint Eustatius, and Saba |
| BFA | Burkina Faso |
| BGD | People's Republic of Bangladesh |
| BGR | Republic of Bulgaria |
| BHR | Kingdom of Bahrain |
| BHS | Commonwealth of The Bahamas |
| BIH | Bosnia and Herzegovina |
| BLM | Saint Barthelemy |
| BLR | Republic of Belarus |
| BLZ | Belize |
| BMU | Bermuda |
| BOL | Plurinational State of Bolivia |
| BRA | Federative Republic of Brazil |
| BRB | Barbados |
| BRN | Brunei Darussalam |
| BTN | Kingdom of Bhutan |
| BVT | Bouvet Island |
| BWA | Republic of Botswana |
| CAF | Central African Republic |
| CAN | Canada |
| CCK | Territory of Cocos (Keeling) Islands |
| CHE | Swiss Confederation |
| CHL | Republic of Chile |
| CHN | People's Republic of China |
| CIV | Republic of Côte d'Ivoire |
| CMR | Republic of Cameroon |
| COD | Democratic Republic of the Congo |
| COG | Republic of the Congo |
| COK | Cook Islands |
| COL | Republic of Colombia |
| COM | Union of the Comoros |
| CPT | Clipperton Island |
| CPV | Republic of Cape Verde |
| CRI | Republic of Costa Rica |
| CUB | Republic of Cuba |
| CUW | Curaçao |
| CXR | Territory of Christmas Island |
| CYM | Cayman Islands |
| CYP | Republic of Cyprus |
| CZE | Czech Republic |
| DEU | Federal Republic of Germany |
| DGA | Diego Garcia |
| DJI | Republic of Djibouti |
| DMA | Commonwealth of Dominica |
| DNK | Kingdom of Denmark |
| DOM | Dominican Republic |
| DZA | People's Democratic Republic of Algeria |
| ECU | Republic of Ecuador |
| EGY | Arab Republic of Egypt |
| ERI | State of Eritrea |
| ESH | Western Sahara |
| ESP | Kingdom of Spain |
| EST | Republic of Estonia |
| ETH | Federal Democratic Republic of Ethiopia |
| FIN | Republic of Finland |
| FJI | Republic of Fiji |
| FLK | Falkland Islands (Islas Malvinas) |
| FRA | French Republic |
| FRO | Faroe Islands |
| FSM | Federated States of Micronesia |
| GAB | Gabonese Republic |
| GBR | United Kingdom of Great Britain and Northern Ireland |
| GEO | Georgia |
| GGY | Bailiwick of Guernsey |
| GHA | Republic of Ghana |
| GIB | Gibraltar |
| GIN | Republic of Guinea |
| GLP | Department of Guadeloupe |
| GMB | Republic of The Gambia |
| GNB | Republic of Guinea-Bissau |
| GNQ | Republic of Equatorial Guinea |
| GRC | Hellenic Republic |
| GRD | Grenada |
| GRL | Greenland |
| GTM | Republic of Guatemala |
| GUF | Department of Guiana |
| GUM | Territory of Guam |
| GUY | Co-operative Republic of Guyana |
| HKG | Hong Kong Special Administrative Region |
| HMD | Territory of Heard Island and McDonald Islands |
| HND | Republic of Honduras |
| HRV | Republic of Croatia |
| HTI | Republic of Haiti |
| HUN | Hungary |
| IDN | Republic of Indonesia |
| IMN | Isle of Man |
| IND | Republic of India |
| IOT | British Indian Ocean Territory |
| IRL | Ireland |
| IRN | Islamic Republic of Iran |
| IRQ | Republic of Iraq |
| ISL | Republic of Iceland |
| ISR | State of Israel |
| ITA | Italian Republic |
| JAM | Jamaica |
| JEY | Bailiwick of Jersey |
| JOR | Hashemite Kingdom of Jordan |
| JPN | Japan |
| KAZ | Republic of Kazakhstan |
| KEN | Republic of Kenya |
| KGZ | Kyrgyz Republic |
| KHM | Kingdom of Cambodia |
| KIR | Republic of Kiribati |
| KNA | Federation of Saint Kitts and Nevis |
| KOR | Republic of Korea |
| KWT | State of Kuwait |
| LAO | Lao People's Democratic Republic |
| LBN | Lebanese Republic |
| LBR | Republic of Liberia |
| LBY | Libya |
| LCA | Saint Lucia |
| LIE | Principality of Liechtenstein |
| LKA | Democratic Socialist Republic of Sri Lanka |
| LSO | Kingdom of Lesotho |
| LTU | Republic of Lithuania |
| LUX | Grand Duchy of Luxembourg |
| LVA | Republic of Latvia |
| MAC | Macau Special Administrative Region |
| MAF | Saint Martin |
| MAR | Kingdom of Morocco |
| MCO | Principality of Monaco |
| MDA | Republic of Moldova |
| MDG | Republic of Madagascar |
| MDV | Republic of Maldives |
| MEX | United Mexican States |
| MHL | Republic of the Marshall Islands |
| MKD | Republic of Macedonia |
| MLI | Republic of Mali |
| MLT | Republic of Malta |
| MMR | Union of Burma |
| MNE | Montenegro |
| MNG | Mongolia |
| MNP | Commonwealth of the Northern Mariana Islands |
| MOZ | Republic of Mozambique |
| MRT | Islamic Republic of Mauritania |
| MSR | Montserrat |
| MTQ | Department of Martinique |
| MUS | Republic of Mauritius |
| MWI | Republic of Malawi |
| MYS | Malaysia |
| MYT | Department of Mayotte |
| NAM | Republic of Namibia |
| NCL | New Caledonia |
| NER | Republic of the Niger |
| NFK | Territory of Norfolk Island |
| NGA | Federal Republic of Nigeria |
| NIC | Republic of Nicaragua |
| NIU | Niue |
| NLD | Kingdom of the Netherlands |
| NOR | Kingdom of Norway |
| NPL | Federal Democratic Republic of Nepal |
| NRU | Republic of Nauru |
| NZL | New Zealand |
| OMN | Sultanate of Oman |
| PAK | Islamic Republic of Pakistan |
| PAN | Republic of Panama |
| PCN | Pitcairn, Henderson, Ducie, and Oeno Islands |
| PER | Republic of Peru |
| PHL | Republic of the Philippines |
| PLW | Republic of Palau |
| PNG | Independent State of Papua New Guinea |
| POL | Republic of Poland |
| PRI | Commonwealth of Puerto Rico |
| PRK | Democratic People's Republic of Korea |
| PRT | Portuguese Republic |
| PRY | Republic of Paraguay |
| PSE | Palestinian Territory |
| PYF | French Polynesia |
| QAT | State of Qatar |
| REU | Department of Reunion |
| ROU | Romania |
| RUS | Russian Federation |
| RWA | Republic of Rwanda |
| SAU | Kingdom of Saudi Arabia |
| SDN | Republic of the Sudan |
| SEN | Republic of Senegal |
| SGP | Republic of Singapore |
| SGS | South Georgia and South Sandwich Islands |
| SHN | Saint Helena, Ascension, and Tristan da Cunha |
| SLB | Solomon Islands |
| SLE | Republic of Sierra Leone |
| SLV | Republic of El Salvador |
| SMR | Republic of San Marino |
| SOM | Somalia, Federal Republic of |
| SPM | Territorial Collectivity of Saint Pierre and Miquelon |
| SRB | Republic of Serbia |
| SSD | Republic of South Sudan |
| STP | Democratic Republic of Sao Tome and Principe |
| SUR | Republic of Suriname |
| SVK | Slovak Republic |
| SVN | Republic of Slovenia |
| SWE | Kingdom of Sweden |
| SWZ | Kingdom of Swaziland |
| SXM | Sint Maarten |
| SYC | Republic of Seychelles |
| SYR | Syrian Arab Republic |
| TCA | Turks and Caicos Islands |
| TCD | Republic of Chad |
| TGO | Togolese Republic |
| THA | Kingdom of Thailand |
| TJK | Republic of Tajikistan |
| TKL | Tokelau |
| TKM | Turkmenistan |
| TLS | Democratic Republic of Timor-Leste |
| TON | Kingdom of Tonga |
| TTO | Republic of Trinidad and Tobago |
| TUN | Tunisian Republic |
| TUR | Republic of Turkey |
| TUV | Tuvalu |
| TWN | Taiwan |
| TZA | United Republic of Tanzania |
| UGA | Republic of Uganda |
| UKR | Ukraine |
| URY | Oriental Republic of Uruguay |
| UZB | Republic of Uzbekistan |
| VAT | State of the Vatican City |
| VCT | Saint Vincent and the Grenadines |
| VEN | Bolivarian Republic of Venezuela |
| VGB | Virgin Islands, British |
| VIR | United States Virgin Islands |
| VNM | Socialist Republic of Vietnam |
| VUT | Republic of Vanuatu |
| WLF | Wallis and Futuna |
| WSM | Independent State of Samoa |
| XAC | Territory of Ashmore and Cartier Islands |
| XAZ | Entity 1 |
| XBI | Bassas da India |
| XBK | Baker Island |
| XCR | Entity 2 |
| XCS | Coral Sea Islands Territory |
| XCY | Entity 3 |
| XEU | Europa Island |
| XGL | Glorioso Islands |
| XGZ | Gaza Strip |
| XHO | Howland Island |
| XJA | Johnston Atoll |
| XJM | Jan Mayen |
| XJN | Juan de Nova Island |
| XJV | Jarvis Island |
| XKM | Entity 4 |
| XKN | Entity 5 |
| XKR | Kingman Reef |
| XKS | Republic of Kosovo |
| XMW | Midway Islands |
| XNV | Navassa Island |
| XPL | Palmyra Atoll |
| XPR | Paracel Islands |
| XQP | Etorofu, Habomai, Kunashiri, and Shikotan Islands  |
| XQZ | Akrotiri |
| XSP | Spratly Islands |
| XSV | Svalbard |
| XTR | Tromelin Island |
| XWB | West Bank |
| XWK | Wake Island |
| XXD | Dhekelia |
| XXX | No Man's Land |
| YEM | Republic of Yemen |
| ZAF | Republic of South Africa |
| ZMB | Republic of Zambia |
| ZWE | Republic of Zimbabwe |
| ACGU | FOUR EYES |
| APFS | Suppressed |
| BWCS | Biological Weapons Convention States |
| CFCK | ROK/US Combined Forces Command, Korea |
| CMFC | Combined Maritime Forces Central |
| CMFP | Cooperative Maritime Forces Pacific |
| CPMT | Civilian Protection Monitoring Team for Sudan |
| CTOC | Countering Transnational Organized Crime |
| CWCS | Chemical Weapons Convention States |
| FVEY | FIVE EYES |
| GCTF | Global Counter-Terrorism Forces |
| GMIF | Global Maritime Interception Forces |
| ISAF | International Security Assistance Force for Afghanistan |
| KFOR | Stabilization Forces in Kosovo |
| MLEC | Multi-Lateral Enduring Contingency |
| NACT | North African Counter-Terrorism Forces |
| NATO | North Atlantic Treaty Organization |
| NCFE | NATO Convention Armed Forces in Europe |
| OSTY | Open Skies Treaty |
| SPAA | Suppressed |
| TEYE | THREE EYES |
| UNCK | United Nations Command, Korea |

### FGI Source Open

The `fgiSourceOpen` attribute (Array[String]) identifies information which qualifies as foreign government information for which the source(s) of the information is not concealed.

The possible patterns for `fgiSourceOpen` are:

| Pattern | description |
| ------- | ----------- |
| NATO/[a-zA-Z\-_] | North Atlantic Treaty Organization Special Words |

The possible values for `fgiSourceOpen` are:

| Value | Description |
| ----- | ----------- |
| ABW | Aruba |
| AFG | Islamic Republic of Afghanistan |
| AGO | Republic of Angola |
| AIA | Anguilla |
| ALB | Republic of Albania |
| AND | Principality of Andorra |
| ARE | United Arab Emirates |
| ARG | Argentine Republic |
| ARM | Republic of Armenia |
| ASM | Territory of American Samoa |
| ATA | Antarctica |
| ATF | French Southern and Antarctic Lands |
| ATG | Antigua and Barbuda |
| AUS | Commonwealth of Australia |
| AUT | Republic of Austria |
| AX1 | Unknown |
| AX2 | Guantanamo Bay Naval Base |
| AZE | Republic of Azerbaijan |
| BDI | Republic of Burundi |
| BEL | Kingdom of Belgium |
| BEN | Republic of Benin |
| BES | Bonaire, Sint Eustatius, and Saba |
| BFA | Burkina Faso |
| BGD | People's Republic of Bangladesh |
| BGR | Republic of Bulgaria |
| BHR | Kingdom of Bahrain |
| BHS | Commonwealth of The Bahamas |
| BIH | Bosnia and Herzegovina |
| BLM | Saint Barthelemy |
| BLR | Republic of Belarus |
| BLZ | Belize |
| BMU | Bermuda |
| BOL | Plurinational State of Bolivia |
| BRA | Federative Republic of Brazil |
| BRB | Barbados |
| BRN | Brunei Darussalam |
| BTN | Kingdom of Bhutan |
| BVT | Bouvet Island |
| BWA | Republic of Botswana |
| CAF | Central African Republic |
| CAN | Canada |
| CCK | Territory of Cocos (Keeling) Islands |
| CHE | Swiss Confederation |
| CHL | Republic of Chile |
| CHN | People's Republic of China |
| CIV | Republic of Côte d'Ivoire |
| CMR | Republic of Cameroon |
| COD | Democratic Republic of the Congo |
| COG | Republic of the Congo |
| COK | Cook Islands |
| COL | Republic of Colombia |
| COM | Union of the Comoros |
| CPT | Clipperton Island |
| CPV | Republic of Cape Verde |
| CRI | Republic of Costa Rica |
| CUB | Republic of Cuba |
| CUW | Curaçao |
| CXR | Territory of Christmas Island |
| CYM | Cayman Islands |
| CYP | Republic of Cyprus |
| CZE | Czech Republic |
| DEU | Federal Republic of Germany |
| DGA | Diego Garcia |
| DJI | Republic of Djibouti |
| DMA | Commonwealth of Dominica |
| DNK | Kingdom of Denmark |
| DOM | Dominican Republic |
| DZA | People's Democratic Republic of Algeria |
| ECU | Republic of Ecuador |
| EGY | Arab Republic of Egypt |
| ERI | State of Eritrea |
| ESH | Western Sahara |
| ESP | Kingdom of Spain |
| EST | Republic of Estonia |
| ETH | Federal Democratic Republic of Ethiopia |
| FIN | Republic of Finland |
| FJI | Republic of Fiji |
| FLK | Falkland Islands (Islas Malvinas) |
| FRA | French Republic |
| FRO | Faroe Islands |
| FSM | Federated States of Micronesia |
| GAB | Gabonese Republic |
| GBR | United Kingdom of Great Britain and Northern Ireland |
| GEO | Georgia |
| GGY | Bailiwick of Guernsey |
| GHA | Republic of Ghana |
| GIB | Gibraltar |
| GIN | Republic of Guinea |
| GLP | Department of Guadeloupe |
| GMB | Republic of The Gambia |
| GNB | Republic of Guinea-Bissau |
| GNQ | Republic of Equatorial Guinea |
| GRC | Hellenic Republic |
| GRD | Grenada |
| GRL | Greenland |
| GTM | Republic of Guatemala |
| GUF | Department of Guiana |
| GUM | Territory of Guam |
| GUY | Co-operative Republic of Guyana |
| HKG | Hong Kong Special Administrative Region |
| HMD | Territory of Heard Island and McDonald Islands |
| HND | Republic of Honduras |
| HRV | Republic of Croatia |
| HTI | Republic of Haiti |
| HUN | Hungary |
| IDN | Republic of Indonesia |
| IMN | Isle of Man |
| IND | Republic of India |
| IOT | British Indian Ocean Territory |
| IRL | Ireland |
| IRN | Islamic Republic of Iran |
| IRQ | Republic of Iraq |
| ISL | Republic of Iceland |
| ISR | State of Israel |
| ITA | Italian Republic |
| JAM | Jamaica |
| JEY | Bailiwick of Jersey |
| JOR | Hashemite Kingdom of Jordan |
| JPN | Japan |
| KAZ | Republic of Kazakhstan |
| KEN | Republic of Kenya |
| KGZ | Kyrgyz Republic |
| KHM | Kingdom of Cambodia |
| KIR | Republic of Kiribati |
| KNA | Federation of Saint Kitts and Nevis |
| KOR | Republic of Korea |
| KWT | State of Kuwait |
| LAO | Lao People's Democratic Republic |
| LBN | Lebanese Republic |
| LBR | Republic of Liberia |
| LBY | Libya |
| LCA | Saint Lucia |
| LIE | Principality of Liechtenstein |
| LKA | Democratic Socialist Republic of Sri Lanka |
| LSO | Kingdom of Lesotho |
| LTU | Republic of Lithuania |
| LUX | Grand Duchy of Luxembourg |
| LVA | Republic of Latvia |
| MAC | Macau Special Administrative Region |
| MAF | Saint Martin |
| MAR | Kingdom of Morocco |
| MCO | Principality of Monaco |
| MDA | Republic of Moldova |
| MDG | Republic of Madagascar |
| MDV | Republic of Maldives |
| MEX | United Mexican States |
| MHL | Republic of the Marshall Islands |
| MKD | Republic of Macedonia |
| MLI | Republic of Mali |
| MLT | Republic of Malta |
| MMR | Union of Burma |
| MNE | Montenegro |
| MNG | Mongolia |
| MNP | Commonwealth of the Northern Mariana Islands |
| MOZ | Republic of Mozambique |
| MRT | Islamic Republic of Mauritania |
| MSR | Montserrat |
| MTQ | Department of Martinique |
| MUS | Republic of Mauritius |
| MWI | Republic of Malawi |
| MYS | Malaysia |
| MYT | Department of Mayotte |
| NAM | Republic of Namibia |
| NCL | New Caledonia |
| NER | Republic of the Niger |
| NFK | Territory of Norfolk Island |
| NGA | Federal Republic of Nigeria |
| NIC | Republic of Nicaragua |
| NIU | Niue |
| NLD | Kingdom of the Netherlands |
| NOR | Kingdom of Norway |
| NPL | Federal Democratic Republic of Nepal |
| NRU | Republic of Nauru |
| NZL | New Zealand |
| OMN | Sultanate of Oman |
| PAK | Islamic Republic of Pakistan |
| PAN | Republic of Panama |
| PCN | Pitcairn, Henderson, Ducie, and Oeno Islands |
| PER | Republic of Peru |
| PHL | Republic of the Philippines |
| PLW | Republic of Palau |
| PNG | Independent State of Papua New Guinea |
| POL | Republic of Poland |
| PRI | Commonwealth of Puerto Rico |
| PRK | Democratic People's Republic of Korea |
| PRT | Portuguese Republic |
| PRY | Republic of Paraguay |
| PSE | Palestinian Territory |
| PYF | French Polynesia |
| QAT | State of Qatar |
| REU | Department of Reunion |
| ROU | Romania |
| RUS | Russian Federation |
| RWA | Republic of Rwanda |
| SAU | Kingdom of Saudi Arabia |
| SDN | Republic of the Sudan |
| SEN | Republic of Senegal |
| SGP | Republic of Singapore |
| SGS | South Georgia and South Sandwich Islands |
| SHN | Saint Helena, Ascension, and Tristan da Cunha |
| SLB | Solomon Islands |
| SLE | Republic of Sierra Leone |
| SLV | Republic of El Salvador |
| SMR | Republic of San Marino |
| SOM | Somalia, Federal Republic of |
| SPM | Territorial Collectivity of Saint Pierre and Miquelon |
| SRB | Republic of Serbia |
| SSD | Republic of South Sudan |
| STP | Democratic Republic of Sao Tome and Principe |
| SUR | Republic of Suriname |
| SVK | Slovak Republic |
| SVN | Republic of Slovenia |
| SWE | Kingdom of Sweden |
| SWZ | Kingdom of Swaziland |
| SXM | Sint Maarten |
| SYC | Republic of Seychelles |
| SYR | Syrian Arab Republic |
| TCA | Turks and Caicos Islands |
| TCD | Republic of Chad |
| TGO | Togolese Republic |
| THA | Kingdom of Thailand |
| TJK | Republic of Tajikistan |
| TKL | Tokelau |
| TKM | Turkmenistan |
| TLS | Democratic Republic of Timor-Leste |
| TON | Kingdom of Tonga |
| TTO | Republic of Trinidad and Tobago |
| TUN | Tunisian Republic |
| TUR | Republic of Turkey |
| TUV | Tuvalu |
| TWN | Taiwan |
| TZA | United Republic of Tanzania |
| UGA | Republic of Uganda |
| UKR | Ukraine |
| URY | Oriental Republic of Uruguay |
| UZB | Republic of Uzbekistan |
| VAT | State of the Vatican City |
| VCT | Saint Vincent and the Grenadines |
| VEN | Bolivarian Republic of Venezuela |
| VGB | Virgin Islands, British |
| VIR | United States Virgin Islands |
| VNM | Socialist Republic of Vietnam |
| VUT | Republic of Vanuatu |
| WLF | Wallis and Futuna |
| WSM | Independent State of Samoa |
| XAC | Territory of Ashmore and Cartier Islands |
| XAZ | Entity 1 |
| XBI | Bassas da India |
| XBK | Baker Island |
| XCR | Entity 2 |
| XCS | Coral Sea Islands Territory |
| XCY | Entity 3 |
| XEU | Europa Island |
| XGL | Glorioso Islands |
| XGZ | Gaza Strip |
| XHO | Howland Island |
| XJA | Johnston Atoll |
| XJM | Jan Mayen |
| XJN | Juan de Nova Island |
| XJV | Jarvis Island |
| XKM | Entity 4 |
| XKN | Entity 5 |
| XKR | Kingman Reef |
| XKS | Republic of Kosovo |
| XMW | Midway Islands |
| XNV | Navassa Island |
| XPL | Palmyra Atoll |
| XPR | Paracel Islands |
| XQP | Etorofu, Habomai, Kunashiri, and Shikotan Islands  |
| XQZ | Akrotiri |
| XSP | Spratly Islands |
| XSV | Svalbard |
| XTR | Tromelin Island |
| XWB | West Bank |
| XWK | Wake Island |
| XXD | Dhekelia |
| XXX | No Man's Land |
| YEM | Republic of Yemen |
| ZAF | Republic of South Africa |
| ZMB | Republic of Zambia |
| ZWE | Republic of Zimbabwe |
| ACGU | FOUR EYES |
| APFS | Suppressed |
| BWCS | Biological Weapons Convention States |
| CFCK | ROK/US Combined Forces Command, Korea |
| CMFC | Combined Maritime Forces Central |
| CMFP | Cooperative Maritime Forces Pacific |
| CPMT | Civilian Protection Monitoring Team for Sudan |
| CTOC | Countering Transnational Organized Crime |
| CWCS | Chemical Weapons Convention States |
| FVEY | FIVE EYES |
| GCTF | Global Counter-Terrorism Forces |
| GMIF | Global Maritime Interception Forces |
| ISAF | International Security Assistance Force for Afghanistan |
| KFOR | Stabilization Forces in Kosovo |
| MLEC | Multi-Lateral Enduring Contingency |
| NACT | North African Counter-Terrorism Forces |
| NATO | North Atlantic Treaty Organization |
| NCFE | NATO Convention Armed Forces in Europe |
| OSTY | Open Skies Treaty |
| SPAA | Suppressed |
| TEYE | THREE EYES |
| UNCK | United Nations Command, Korea |

### FGI Source Protected

The `fgiSourceProtected` attribute (Array[String]) has unique specific rules concerning its usage. A single indicator that information qualifies as foreign government information for which the source(s) of the information must be concealed. Within protected internal organizational spaces this element may be used to maintain a record of the one or more indicators identifying information which qualifies as foreign government information for which the source(s) of the information must be concealed. Measures must be taken prior to dissemination of the information to conceal the source(s) of the foreign government information. An indication that information qualifies as foreign government information according to CAPCO guidelines for which the source(s) of the information must be concealed when the information is disseminated in shared spaces This data element has a dual purpose. Within shared spaces, the data element serves only to indicate the presence of information which is categorized as foreign government information according to CAPCO guidelines for which the source(s) of the information is concealed, in which case, this data element's value will always be `FGI`. The data element may also be employed in this manner within protected internal organizational spaces. However, within protected internal organizational spaces this data element may alternatively be used to maintain a formal record of the foreign country or countries and/or registered international organization(s) that are the non-disclosable owner(s) and/or producer(s) of information which is categorized as foreign government information according to CAPCO guidelines for which the source(s) of the information must be concealed when the resource is disseminated to shared spaces. If the data element is employed in this manner, then additional measures must be taken prior to dissemination of the resource to shared spaces so that any indications of the non-disclosable owner(s) and/or producer(s) of information within the resource are eliminated. In all cases, the corresponding portion marking or banner marking should be compliant with CAPCO guidelines for FGI when the source must be concealed. In other words, even if the data element is being employed within protected internal organizational spaces to maintain a formal record of the non-disclosable owner(s) and/or producer(s), if the resource is rendered for display within the protected internal organizational spaces in any format by a stylesheet or as a result of any other transformation process, then the non-disclosable owner(s) and/or producer(s) should not be included in the corresponding portion marking or banner marking.

The possible patterns for `fgiSourceProtected` are:

| Pattern | description |
| ------- | ----------- |
| NATO/[a-zA-Z\-_] | North Atlantic Treaty Organization Special Words |

The possible values for `fgiSourceProtected` are:

| Value | Description |
| ----- | ----------- |
| FGI | Foreign Government Information |
| ABW | Aruba |
| AFG | Islamic Republic of Afghanistan |
| AGO | Republic of Angola |
| AIA | Anguilla |
| ALB | Republic of Albania |
| AND | Principality of Andorra |
| ARE | United Arab Emirates |
| ARG | Argentine Republic |
| ARM | Republic of Armenia |
| ASM | Territory of American Samoa |
| ATA | Antarctica |
| ATF | French Southern and Antarctic Lands |
| ATG | Antigua and Barbuda |
| AUS | Commonwealth of Australia |
| AUT | Republic of Austria |
| AX2 | Guantanamo Bay Naval Base |
| AZE | Republic of Azerbaijan |
| BDI | Republic of Burundi |
| BEL | Kingdom of Belgium |
| BEN | Republic of Benin |
| BES | Bonaire, Sint Eustatius, and Saba |
| BFA | Burkina Faso |
| BGD | People's Republic of Bangladesh |
| BGR | Republic of Bulgaria |
| BHR | Kingdom of Bahrain |
| BHS | Commonwealth of The Bahamas |
| BIH | Bosnia and Herzegovina |
| BLM | Saint Barthelemy |
| BLR | Republic of Belarus |
| BLZ | Belize |
| BMU | Bermuda |
| BOL | Plurinational State of Bolivia |
| BRA | Federative Republic of Brazil |
| BRB | Barbados |
| BRN | Brunei Darussalam |
| BTN | Kingdom of Bhutan |
| BVT | Bouvet Island |
| BWA | Republic of Botswana |
| CAF | Central African Republic |
| CAN | Canada |
| CCK | Territory of Cocos (Keeling) Islands |
| CHE | Swiss Confederation |
| CHL | Republic of Chile |
| CHN | People's Republic of China |
| CIV | Republic of Côte d'Ivoire |
| CMR | Republic of Cameroon |
| COD | Democratic Republic of the Congo |
| COG | Republic of the Congo |
| COK | Cook Islands |
| COL | Republic of Colombia |
| COM | Union of the Comoros |
| CPT | Clipperton Island |
| CPV | Republic of Cape Verde |
| CRI | Republic of Costa Rica |
| CUB | Republic of Cuba |
| CUW | Curaçao |
| CXR | Territory of Christmas Island |
| CYM | Cayman Islands |
| CYP | Republic of Cyprus |
| CZE | Czech Republic |
| DEU | Federal Republic of Germany |
| DGA | Diego Garcia |
| DJI | Republic of Djibouti |
| DMA | Commonwealth of Dominica |
| DNK | Kingdom of Denmark |
| DOM | Dominican Republic |
| DZA | People's Democratic Republic of Algeria |
| ECU | Republic of Ecuador |
| EGY | Arab Republic of Egypt |
| ERI | State of Eritrea |
| ESH | Western Sahara |
| ESP | Kingdom of Spain |
| EST | Republic of Estonia |
| ETH | Federal Democratic Republic of Ethiopia |
| FIN | Republic of Finland |
| FJI | Republic of Fiji |
| FLK | Falkland Islands (Islas Malvinas) |
| FRA | French Republic |
| FRO | Faroe Islands |
| FSM | Federated States of Micronesia |
| GAB | Gabonese Republic |
| GBR | United Kingdom of Great Britain and Northern Ireland |
| GEO | Georgia |
| GGY | Bailiwick of Guernsey |
| GHA | Republic of Ghana |
| GIB | Gibraltar |
| GIN | Republic of Guinea |
| GLP | Department of Guadeloupe |
| GMB | Republic of The Gambia |
| GNB | Republic of Guinea-Bissau |
| GNQ | Republic of Equatorial Guinea |
| GRC | Hellenic Republic |
| GRD | Grenada |
| GRL | Greenland |
| GTM | Republic of Guatemala |
| GUF | Department of Guiana |
| GUM | Territory of Guam |
| GUY | Co-operative Republic of Guyana |
| HKG | Hong Kong Special Administrative Region |
| HMD | Territory of Heard Island and McDonald Islands |
| HND | Republic of Honduras |
| HRV | Republic of Croatia |
| HTI | Republic of Haiti |
| HUN | Hungary |
| IDN | Republic of Indonesia |
| IMN | Isle of Man |
| IND | Republic of India |
| IOT | British Indian Ocean Territory |
| IRL | Ireland |
| IRN | Islamic Republic of Iran |
| IRQ | Republic of Iraq |
| ISL | Republic of Iceland |
| ISR | State of Israel |
| ITA | Italian Republic |
| JAM | Jamaica |
| JEY | Bailiwick of Jersey |
| JOR | Hashemite Kingdom of Jordan |
| JPN | Japan |
| KAZ | Republic of Kazakhstan |
| KEN | Republic of Kenya |
| KGZ | Kyrgyz Republic |
| KHM | Kingdom of Cambodia |
| KIR | Republic of Kiribati |
| KNA | Federation of Saint Kitts and Nevis |
| KOR | Republic of Korea |
| KWT | State of Kuwait |
| LAO | Lao People's Democratic Republic |
| LBN | Lebanese Republic |
| LBR | Republic of Liberia |
| LBY | Libya |
| LCA | Saint Lucia |
| LIE | Principality of Liechtenstein |
| LKA | Democratic Socialist Republic of Sri Lanka |
| LSO | Kingdom of Lesotho |
| LTU | Republic of Lithuania |
| LUX | Grand Duchy of Luxembourg |
| LVA | Republic of Latvia |
| MAC | Macau Special Administrative Region |
| MAF | Saint Martin |
| MAR | Kingdom of Morocco |
| MCO | Principality of Monaco |
| MDA | Republic of Moldova |
| MDG | Republic of Madagascar |
| MDV | Republic of Maldives |
| MEX | United Mexican States |
| MHL | Republic of the Marshall Islands |
| MKD | Republic of Macedonia |
| MLI | Republic of Mali |
| MLT | Republic of Malta |
| MMR | Union of Burma |
| MNE | Montenegro |
| MNG | Mongolia |
| MNP | Commonwealth of the Northern Mariana Islands |
| MOZ | Republic of Mozambique |
| MRT | Islamic Republic of Mauritania |
| MSR | Montserrat |
| MTQ | Department of Martinique |
| MUS | Republic of Mauritius |
| MWI | Republic of Malawi |
| MYS | Malaysia |
| MYT | Department of Mayotte |
| NAM | Republic of Namibia |
| NCL | New Caledonia |
| NER | Republic of the Niger |
| NFK | Territory of Norfolk Island |
| NGA | Federal Republic of Nigeria |
| NIC | Republic of Nicaragua |
| NIU | Niue |
| NLD | Kingdom of the Netherlands |
| NOR | Kingdom of Norway |
| NPL | Federal Democratic Republic of Nepal |
| NRU | Republic of Nauru |
| NZL | New Zealand |
| OMN | Sultanate of Oman |
| PAK | Islamic Republic of Pakistan |
| PAN | Republic of Panama |
| PCN | Pitcairn, Henderson, Ducie, and Oeno Islands |
| PER | Republic of Peru |
| PHL | Republic of the Philippines |
| PLW | Republic of Palau |
| PNG | Independent State of Papua New Guinea |
| POL | Republic of Poland |
| PRI | Commonwealth of Puerto Rico |
| PRK | Democratic People's Republic of Korea |
| PRT | Portuguese Republic |
| PRY | Republic of Paraguay |
| PSE | Palestinian Territory |
| PYF | French Polynesia |
| QAT | State of Qatar |
| REU | Department of Reunion |
| ROU | Romania |
| RUS | Russian Federation |
| RWA | Republic of Rwanda |
| SAU | Kingdom of Saudi Arabia |
| SDN | Republic of the Sudan |
| SEN | Republic of Senegal |
| SGP | Republic of Singapore |
| SGS | South Georgia and South Sandwich Islands |
| SHN | Saint Helena, Ascension, and Tristan da Cunha |
| SLB | Solomon Islands |
| SLE | Republic of Sierra Leone |
| SLV | Republic of El Salvador |
| SMR | Republic of San Marino |
| SOM | Somalia, Federal Republic of |
| SPM | Territorial Collectivity of Saint Pierre and Miquelon |
| SRB | Republic of Serbia |
| SSD | Republic of South Sudan |
| STP | Democratic Republic of Sao Tome and Principe |
| SUR | Republic of Suriname |
| SVK | Slovak Republic |
| SVN | Republic of Slovenia |
| SWE | Kingdom of Sweden |
| SWZ | Kingdom of Swaziland |
| SXM | Sint Maarten |
| SYC | Republic of Seychelles |
| SYR | Syrian Arab Republic |
| TCA | Turks and Caicos Islands |
| TCD | Republic of Chad |
| TGO | Togolese Republic |
| THA | Kingdom of Thailand |
| TJK | Republic of Tajikistan |
| TKL | Tokelau |
| TKM | Turkmenistan |
| TLS | Democratic Republic of Timor-Leste |
| TON | Kingdom of Tonga |
| TTO | Republic of Trinidad and Tobago |
| TUN | Tunisian Republic |
| TUR | Republic of Turkey |
| TUV | Tuvalu |
| TWN | Taiwan |
| TZA | United Republic of Tanzania |
| UGA | Republic of Uganda |
| UKR | Ukraine |
| URY | Oriental Republic of Uruguay |
| UZB | Republic of Uzbekistan |
| VAT | State of the Vatican City |
| VCT | Saint Vincent and the Grenadines |
| VEN | Bolivarian Republic of Venezuela |
| VGB | Virgin Islands, British |
| VIR | United States Virgin Islands |
| VNM | Socialist Republic of Vietnam |
| VUT | Republic of Vanuatu |
| WLF | Wallis and Futuna |
| WSM | Independent State of Samoa |
| XAC | Territory of Ashmore and Cartier Islands |
| XAZ | Entity 1 |
| XBI | Bassas da India |
| XBK | Baker Island |
| XCR | Entity 2 |
| XCS | Coral Sea Islands Territory |
| XCY | Entity 3 |
| XEU | Europa Island |
| XGL | Glorioso Islands |
| XGZ | Gaza Strip |
| XHO | Howland Island |
| XJA | Johnston Atoll |
| XJM | Jan Mayen |
| XJN | Juan de Nova Island |
| XJV | Jarvis Island |
| XKM | Entity 4 |
| XKN | Entity 5 |
| XKR | Kingman Reef |
| XKS | Republic of Kosovo |
| XMW | Midway Islands |
| XNV | Navassa Island |
| XPL | Palmyra Atoll |
| XPR | Paracel Islands |
| XQP | Etorofu, Habomai, Kunashiri, and Shikotan Islands  |
| XQZ | Akrotiri |
| XSP | Spratly Islands |
| XSV | Svalbard |
| XTR | Tromelin Island |
| XWB | West Bank |
| XWK | Wake Island |
| XXD | Dhekelia |
| XXX | No Man's Land |
| YEM | Republic of Yemen |
| ZAF | Republic of South Africa |
| ZMB | Republic of Zambia |
| ZWE | Republic of Zimbabwe |
| ACGU | FOUR EYES |
| APFS | Suppressed |
| BWCS | Biological Weapons Convention States |
| CFCK | ROK/US Combined Forces Command, Korea |
| CMFC | Combined Maritime Forces Central |
| CMFP | Cooperative Maritime Forces Pacific |
| CPMT | Civilian Protection Monitoring Team for Sudan |
| CTOC | Countering Transnational Organized Crime |
| CWCS | Chemical Weapons Convention States |
| FVEY | FIVE EYES |
| GCTF | Global Counter-Terrorism Forces |
| GMIF | Global Maritime Interception Forces |
| ISAF | International Security Assistance Force for Afghanistan |
| KFOR | Stabilization Forces in Kosovo |
| MLEC | Multi-Lateral Enduring Contingency |
| NACT | North African Counter-Terrorism Forces |
| NATO | North Atlantic Treaty Organization |
| NCFE | NATO Convention Armed Forces in Europe |
| OSTY | Open Skies Treaty |
| SPAA | Suppressed |
| TEYE | THREE EYES |
| UNCK | United Nations Command, Korea |

### Releasable To

The `releasableTo` attribute (Array[String]) identifies one or more countries and/or international organizations to which classified information may be released based on the determination of an originator in accordance with established foreign disclosure procedures.  This attribute is used in conjunction with the `disseminationControls` attribute.

The possible patterns for `releasableTo` are:

| Pattern | description |
| ------- | ----------- |
| NATO/[a-zA-Z\-_] | North Atlantic Treaty Organization Special Words |

The possible values for `releasableTo` are:

| Value | Description |
| ----- | ----------- |
| USA | United States |
| ABW | Aruba |
| AFG | Islamic Republic of Afghanistan |
| AGO | Republic of Angola |
| AIA | Anguilla |
| ALB | Republic of Albania |
| AND | Principality of Andorra |
| ARE | United Arab Emirates |
| ARG | Argentine Republic |
| ARM | Republic of Armenia |
| ASM | Territory of American Samoa |
| ATA | Antarctica |
| ATF | French Southern and Antarctic Lands |
| ATG | Antigua and Barbuda |
| AUS | Commonwealth of Australia |
| AUT | Republic of Austria |
| AX2 | Guantanamo Bay Naval Base |
| AZE | Republic of Azerbaijan |
| BDI | Republic of Burundi |
| BEL | Kingdom of Belgium |
| BEN | Republic of Benin |
| BES | Bonaire, Sint Eustatius, and Saba |
| BFA | Burkina Faso |
| BGD | People's Republic of Bangladesh |
| BGR | Republic of Bulgaria |
| BHR | Kingdom of Bahrain |
| BHS | Commonwealth of The Bahamas |
| BIH | Bosnia and Herzegovina |
| BLM | Saint Barthelemy |
| BLR | Republic of Belarus |
| BLZ | Belize |
| BMU | Bermuda |
| BOL | Plurinational State of Bolivia |
| BRA | Federative Republic of Brazil |
| BRB | Barbados |
| BRN | Brunei Darussalam |
| BTN | Kingdom of Bhutan |
| BVT | Bouvet Island |
| BWA | Republic of Botswana |
| CAF | Central African Republic |
| CAN | Canada |
| CCK | Territory of Cocos (Keeling) Islands |
| CHE | Swiss Confederation |
| CHL | Republic of Chile |
| CHN | People's Republic of China |
| CIV | Republic of Côte d'Ivoire |
| CMR | Republic of Cameroon |
| COD | Democratic Republic of the Congo |
| COG | Republic of the Congo |
| COK | Cook Islands |
| COL | Republic of Colombia |
| COM | Union of the Comoros |
| CPT | Clipperton Island |
| CPV | Republic of Cape Verde |
| CRI | Republic of Costa Rica |
| CUB | Republic of Cuba |
| CUW | Curaçao |
| CXR | Territory of Christmas Island |
| CYM | Cayman Islands |
| CYP | Republic of Cyprus |
| CZE | Czech Republic |
| DEU | Federal Republic of Germany |
| DGA | Diego Garcia |
| DJI | Republic of Djibouti |
| DMA | Commonwealth of Dominica |
| DNK | Kingdom of Denmark |
| DOM | Dominican Republic |
| DZA | People's Democratic Republic of Algeria |
| ECU | Republic of Ecuador |
| EGY | Arab Republic of Egypt |
| ERI | State of Eritrea |
| ESH | Western Sahara |
| ESP | Kingdom of Spain |
| EST | Republic of Estonia |
| ETH | Federal Democratic Republic of Ethiopia |
| FIN | Republic of Finland |
| FJI | Republic of Fiji |
| FLK | Falkland Islands (Islas Malvinas) |
| FRA | French Republic |
| FRO | Faroe Islands |
| FSM | Federated States of Micronesia |
| GAB | Gabonese Republic |
| GBR | United Kingdom of Great Britain and Northern Ireland |
| GEO | Georgia |
| GGY | Bailiwick of Guernsey |
| GHA | Republic of Ghana |
| GIB | Gibraltar |
| GIN | Republic of Guinea |
| GLP | Department of Guadeloupe |
| GMB | Republic of The Gambia |
| GNB | Republic of Guinea-Bissau |
| GNQ | Republic of Equatorial Guinea |
| GRC | Hellenic Republic |
| GRD | Grenada |
| GRL | Greenland |
| GTM | Republic of Guatemala |
| GUF | Department of Guiana |
| GUM | Territory of Guam |
| GUY | Co-operative Republic of Guyana |
| HKG | Hong Kong Special Administrative Region |
| HMD | Territory of Heard Island and McDonald Islands |
| HND | Republic of Honduras |
| HRV | Republic of Croatia |
| HTI | Republic of Haiti |
| HUN | Hungary |
| IDN | Republic of Indonesia |
| IMN | Isle of Man |
| IND | Republic of India |
| IOT | British Indian Ocean Territory |
| IRL | Ireland |
| IRN | Islamic Republic of Iran |
| IRQ | Republic of Iraq |
| ISL | Republic of Iceland |
| ISR | State of Israel |
| ITA | Italian Republic |
| JAM | Jamaica |
| JEY | Bailiwick of Jersey |
| JOR | Hashemite Kingdom of Jordan |
| JPN | Japan |
| KAZ | Republic of Kazakhstan |
| KEN | Republic of Kenya |
| KGZ | Kyrgyz Republic |
| KHM | Kingdom of Cambodia |
| KIR | Republic of Kiribati |
| KNA | Federation of Saint Kitts and Nevis |
| KOR | Republic of Korea |
| KWT | State of Kuwait |
| LAO | Lao People's Democratic Republic |
| LBN | Lebanese Republic |
| LBR | Republic of Liberia |
| LBY | Libya |
| LCA | Saint Lucia |
| LIE | Principality of Liechtenstein |
| LKA | Democratic Socialist Republic of Sri Lanka |
| LSO | Kingdom of Lesotho |
| LTU | Republic of Lithuania |
| LUX | Grand Duchy of Luxembourg |
| LVA | Republic of Latvia |
| MAC | Macau Special Administrative Region |
| MAF | Saint Martin |
| MAR | Kingdom of Morocco |
| MCO | Principality of Monaco |
| MDA | Republic of Moldova |
| MDG | Republic of Madagascar |
| MDV | Republic of Maldives |
| MEX | United Mexican States |
| MHL | Republic of the Marshall Islands |
| MKD | Republic of Macedonia |
| MLI | Republic of Mali |
| MLT | Republic of Malta |
| MMR | Union of Burma |
| MNE | Montenegro |
| MNG | Mongolia |
| MNP | Commonwealth of the Northern Mariana Islands |
| MOZ | Republic of Mozambique |
| MRT | Islamic Republic of Mauritania |
| MSR | Montserrat |
| MTQ | Department of Martinique |
| MUS | Republic of Mauritius |
| MWI | Republic of Malawi |
| MYS | Malaysia |
| MYT | Department of Mayotte |
| NAM | Republic of Namibia |
| NCL | New Caledonia |
| NER | Republic of the Niger |
| NFK | Territory of Norfolk Island |
| NGA | Federal Republic of Nigeria |
| NIC | Republic of Nicaragua |
| NIU | Niue |
| NLD | Kingdom of the Netherlands |
| NOR | Kingdom of Norway |
| NPL | Federal Democratic Republic of Nepal |
| NRU | Republic of Nauru |
| NZL | New Zealand |
| OMN | Sultanate of Oman |
| PAK | Islamic Republic of Pakistan |
| PAN | Republic of Panama |
| PCN | Pitcairn, Henderson, Ducie, and Oeno Islands |
| PER | Republic of Peru |
| PHL | Republic of the Philippines |
| PLW | Republic of Palau |
| PNG | Independent State of Papua New Guinea |
| POL | Republic of Poland |
| PRI | Commonwealth of Puerto Rico |
| PRK | Democratic People's Republic of Korea |
| PRT | Portuguese Republic |
| PRY | Republic of Paraguay |
| PSE | Palestinian Territory |
| PYF | French Polynesia |
| QAT | State of Qatar |
| REU | Department of Reunion |
| ROU | Romania |
| RUS | Russian Federation |
| RWA | Republic of Rwanda |
| SAU | Kingdom of Saudi Arabia |
| SDN | Republic of the Sudan |
| SEN | Republic of Senegal |
| SGP | Republic of Singapore |
| SGS | South Georgia and South Sandwich Islands |
| SHN | Saint Helena, Ascension, and Tristan da Cunha |
| SLB | Solomon Islands |
| SLE | Republic of Sierra Leone |
| SLV | Republic of El Salvador |
| SMR | Republic of San Marino |
| SOM | Somalia, Federal Republic of |
| SPM | Territorial Collectivity of Saint Pierre and Miquelon |
| SRB | Republic of Serbia |
| SSD | Republic of South Sudan |
| STP | Democratic Republic of Sao Tome and Principe |
| SUR | Republic of Suriname |
| SVK | Slovak Republic |
| SVN | Republic of Slovenia |
| SWE | Kingdom of Sweden |
| SWZ | Kingdom of Swaziland |
| SXM | Sint Maarten |
| SYC | Republic of Seychelles |
| SYR | Syrian Arab Republic |
| TCA | Turks and Caicos Islands |
| TCD | Republic of Chad |
| TGO | Togolese Republic |
| THA | Kingdom of Thailand |
| TJK | Republic of Tajikistan |
| TKL | Tokelau |
| TKM | Turkmenistan |
| TLS | Democratic Republic of Timor-Leste |
| TON | Kingdom of Tonga |
| TTO | Republic of Trinidad and Tobago |
| TUN | Tunisian Republic |
| TUR | Republic of Turkey |
| TUV | Tuvalu |
| TWN | Taiwan |
| TZA | United Republic of Tanzania |
| UGA | Republic of Uganda |
| UKR | Ukraine |
| URY | Oriental Republic of Uruguay |
| UZB | Republic of Uzbekistan |
| VAT | State of the Vatican City |
| VCT | Saint Vincent and the Grenadines |
| VEN | Bolivarian Republic of Venezuela |
| VGB | Virgin Islands, British |
| VIR | United States Virgin Islands |
| VNM | Socialist Republic of Vietnam |
| VUT | Republic of Vanuatu |
| WLF | Wallis and Futuna |
| WSM | Independent State of Samoa |
| XAC | Territory of Ashmore and Cartier Islands |
| XAZ | Entity 1 |
| XBI | Bassas da India |
| XBK | Baker Island |
| XCR | Entity 2 |
| XCS | Coral Sea Islands Territory |
| XCY | Entity 3 |
| XEU | Europa Island |
| XGL | Glorioso Islands |
| XGZ | Gaza Strip |
| XHO | Howland Island |
| XJA | Johnston Atoll |
| XJM | Jan Mayen |
| XJN | Juan de Nova Island |
| XJV | Jarvis Island |
| XKM | Entity 4 |
| XKN | Entity 5 |
| XKR | Kingman Reef |
| XKS | Republic of Kosovo |
| XMW | Midway Islands |
| XNV | Navassa Island |
| XPL | Palmyra Atoll |
| XPR | Paracel Islands |
| XQP | Etorofu, Habomai, Kunashiri, and Shikotan Islands  |
| XQZ | Akrotiri |
| XSP | Spratly Islands |
| XSV | Svalbard |
| XTR | Tromelin Island |
| XWB | West Bank |
| XWK | Wake Island |
| XXD | Dhekelia |
| XXX | No Man's Land |
| YEM | Republic of Yemen |
| ZAF | Republic of South Africa |
| ZMB | Republic of Zambia |
| ZWE | Republic of Zimbabwe |
| ACGU | FOUR EYES |
| APFS | Suppressed |
| BWCS | Biological Weapons Convention States |
| CFCK | ROK/US Combined Forces Command, Korea |
| CMFC | Combined Maritime Forces Central |
| CMFP | Cooperative Maritime Forces Pacific |
| CPMT | Civilian Protection Monitoring Team for Sudan |
| CTOC | Countering Transnational Organized Crime |
| CWCS | Chemical Weapons Convention States |
| FVEY | FIVE EYES |
| GCTF | Global Counter-Terrorism Forces |
| GMIF | Global Maritime Interception Forces |
| ISAF | International Security Assistance Force for Afghanistan |
| KFOR | Stabilization Forces in Kosovo |
| MLEC | Multi-Lateral Enduring Contingency |
| NACT | North African Counter-Terrorism Forces |
| NATO | North Atlantic Treaty Organization |
| NCFE | NATO Convention Armed Forces in Europe |
| OSTY | Open Skies Treaty |
| SPAA | Suppressed |
| TEYE | THREE EYES |
| UNCK | United Nations Command, Korea |

### Non-IC Markings

The `nonICmarkings` attribute (Array[String]) identifies one or more indicators for expanding or limiting the distribution of information originating from non-intelligence components.

The possible patterns for `nonICmarkings` are:

| Pattern | Description |
| ------- | ----------- |
| ACCM-[A-Z0-9\-_]{1,61} | The name of the ALTERNATE COMPENSATORY CONTROL MEASURE, substituting "_" for a space |
| NNPI | NAVAL NUCLEAR PROPULSION INFORMATION |

The possible values for `nonICmarkings` are:

| Value | Description |
| ----- | ----------- |
| DS | LIMITED DISTRIBUTION |
| XD | EXCLUSIVE DISTRIBUTION |
| ND | NO DISTRIBUTION |
| SBU | SENSITIVE BUT UNCLASSIFIED |
| SBU-NF | SENSITIVE BUT UNCLASSIFIED NOFORN |
| LES | LAW ENFORCEMENT SENSITIVE |
| LES-NF | LAW ENFORCEMENT SENSITIVE NOFORN |
| SSI | SENSITIVE SECURITY INFORMATION |

### Classified By

The `classifiedBy` attribute (String) is used primarily at the resource level. The identity, by name or personal identifier, and position title of the original classification authority for a resource. It is manifested only in the 'Classified By' line of a resource's classification authority block.

### Compilation Reason

The `compilationReason` attribute (String) is a description of the reasons that the classification of this element is more restrictive than a simple roll-up of the sub elements would result in. This acts as an indicator to rule engines that there is not accidental over classification going on and to users that special care beyond what the portion marks reveal must be taken when using this data. Use of this mark does not replace the need for the compilation reason being defined in the prose in accordance with ISOO Directive 1. For example this would document why 3 Unclassified bullet items form a Secret List. Without this reason being noted the above described document would be considered to be miss-marked and overclassified.

### Derivatively Classified By

The `derivativelyClassifiedBy` attribute (String) is used primarily at the resource level. The identity, by name or personal identifier, of the derivative classification authority. It is manifested only in the 'Classified By' line of a resource's classification authority block.

### Classification Reason

The `classificationReason` attribute (String) is used primarily at the resource level. One or more reason indicators or explanatory text describing the basis for an original classification decision. It is manifested only in the 'Reason' line of a resource's classification authority block.

### Non-US Controls

The `nonUSControls` attribute (Array[String]) identifies one or more indicators for expanding or limiting the distribution of information originating from non-US components.

The possible values for `nonUSControls` are:

| Value | Description |
| ----- | ----------- |
| ATOMAL | NATO Atomal mark |
| BOHEMIA | NATO Bohemia mark |
| BALK | NATO Balk mark |

### Derived From

The `derivedFrom` attribute (String) is used primarily at the resource level. A citation of the authoritative source or reference to multiple sources of the classification markings used in a classified resource. It is manifested only in the 'Derived From' line of a document's classification authority block. ISOO's guidance is: Source of derivative classification. (1) The derivative classifier shall concisely identify the source document or the classification guide on the ‘‘Derived From’’ line, including the agency and, where available, the office of origin, and the date of the source or guide. An example might appear as: Derived From: Memo, ‘‘Funding Problems,’’ October 20, 2008, Office of Administration, Department of Good Works or Derived From: CG No. 1, Department of Good Works, dated October 20, 2008 (i) When a document is classified derivatively on the basis of more than one source document or classification guide, the ‘‘Derived From’’ line shall appear as: Derived From: Multiple Sources (ii) The derivative classifier shall include a listing of the source materials on, or attached to, each derivatively classified document.

### Declassification Date

The `declassDate` attribute (Date) is used primarily at the resource level. A specific year, month, and day upon which the information shall be automatically declassified if not properly exempted from automatic declassification. It is manifested in the 'Declassify On' line of a resource's classification authority block.

### Declassification Event

The `declassEvent` attribute (String) is used primarily at the resource level. A description of an event upon which the information shall be automatically declassified if not properly exempted from automatic declassification. It is manifested only in the 'Declassify On' line of a resource's classification authority block.

### Declassification Exception

The `declassException` attribute (String) is used primarily at the resource level. A single indicator describing an exemption to the nominal 25-year point for automatic declassification. This element is used in conjunction with the `declassDate` or `declassEvent`. It is manifested in the 'Declassify On' line of a resource's classification authority block. ISOO has stated it should be a SINGLE value giving the longest protection.

The possible values for `declassException` are:

| Value | Description |
| ----- | ----------- |
| AEA |  When using a source document that contains portions of Restricted Data (RD) or Formerly Restricted Data (FRD) where the RD/FRD source document(s) do not have declassification instructions, the derivatively classified document shall not contain a declassification date or event on the Declassify On line. The following shall be annotated on the Declassify On line: "Not Applicable or (N/A) to RD/FRD portions" and "See source list for NSI portions" separated by a period. The source list must include the declassification instruction for each of the source documents classified under E.O. 13526 and shall not appear in the classification authority block  |
| NATO |  Since NATO information is not to be declassified or downgraded without the prior consent of NATO, the “Declassify on” line of documents that commingle information classified by NATO and U.S. classified NSI, will read “N/A to NATO portions. See source list for NSI portions.” The NSI source list will appear beneath the classification authority block in a manner that clearly identifies it as separate and distinct.  |
| NATO-AEA |  Handles special case of BOTH NATO and AEA as a single exemption.  |
| 25X1 |  Reveal the identity of a confidential human source, a human intelligence source, a relationship with an intelligence or security service of a foreign government or international organization, or a non-human intelligence source; or impair the effectiveness of an intelligence method currently in use, available for use, or under development.  |
| 25X1-EO-12951 |  "25X1, EO 12951" (prescribed by the DNI for use on information described in E.O. 12951, Release of Imagery Acquired by Space-Based National Intelligence Reconnaissance Systems)  |
| 25X2 |  Reveal information that would assist in the development, production, or use of weapons of mass destruction.  |
| 25X3 |  Reveal information that would impair U.S. cryptologic systems or activities.  |
| 25X4 |  Reveal information that would impair the application of state-of-the-art technology within a U.S. weapon system.  |
| 25X5 |  Reveal formally named or numbered U.S. military war plans that remain in effect, or reveal operational or tactical elements of prior plans that are contained in such active plans; |
| 25X6 |  Reveal information, including foreign government information, that would cause serious harm to relations between the United States and a foreign government, or to ongoing diplomatic activities of the United States  |
| 25X7 |  Reveal information that would impair the current ability of United States Government officials to protect the President, Vice President, and other protectees for whom protection services, in the interest of the national security, are authorized.  |
| 25X8 |  Reveal information that would seriously impair current national security emergency preparedness plans or reveal current vulnerabilities of systems, installations, or infrastructures relating to the national security.  |
| 25X9 |  Violate a statute, treaty, or international agreement that does not permit the automatic or unilateral declassification of information at 25 years.  |
| 50X1-HUM |  When the information clearly and demonstrably could be expected to reveal the identity of a confidential human source or a human intelligence source.  |
| 50X1 |  The ISCAP has authorized use of this code in the FBI’s classification guidance (which results in a 75-year classification period) for any agency sourcing/reusing the information.  |
| 50X2-WMD |  When the information clearly and demonstrably could reveal key design concepts of weapons of mass destruction.  |
| 50X6 |  The ISCAP has authorized use of this code in the FBI’s classification guidance (which results in a 75-year classification period) for any agency sourcing/reusing the information.  |
