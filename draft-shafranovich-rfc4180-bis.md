---
title: Common Format and MIME Type for Comma-Separated Values (CSV) Files
abbrev: rfc4180-bis
docname: draft-shafranovich-rfc4180-bis-00
ipr: trust200902
cat: info
pi:
  sortrefs: 'yes'
  strict: 'yes'
  symrefs: 'yes'
  toc: 'yes'
author:
- ins: Y. Shafranovich
  name: Yakov Shafranovich
  organization: Nightwatch Cybersecurity
  email: yakov+ietf@nightwatchcybersecurity.com
  
informative:
  ART:
    title: The Art of Unix Programming, Chapter 5
    author:
        name: E. Raymond
    date: September 2003
    target: http://www.catb.org/~esr/writings/taoup/html/ch05s02.html
  CREATIVYST:
    title: 'HOW-TO: The Comma Separated Value (CSV) File Format'
    author:
        name: J. Repici
        org: Creativyst, Inc.
    date: 2010
    target: http://www.creativyst.com/Doc/Articles/CSV/CSV01.htm
  CSVW:
    title: CSV on the Web Working Group
    author:
        org: W3C
    date: 2016
    target: https://www.w3.org/2013/csvw/wiki/Main_Page
  EDOCEO:
    title: Comma Separated Values (CSV) Standard File Format
    author:
        org: Edoceo, Inc.
    date: 2020
    target: https://edoceo.com/dev/csv-file-format

--- abstract
This RFC documents the common format used for Comma-Separated Values (CSV)
files and updates the associated MIME type "text/csv".

--- middle

# Introduction
The comma separated values format (CSV) has been used as a common way
to exchange data between disparate systems and applications for many years.
Surprisingly, while this format is very popular, it has never been formally
documented and didn't have a media type registered. This was addressed in 2005 via publication
of {{!RFC4180}} and the concurrent registration of the "text/csv" media type.
 
Since the publication of {{!RFC4180}}, the CSV format has evolved and this specification
seeks to reflect these changes as well as update the "text/csv" media type registration.

## Terminology
The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
"OPTIONAL" in this document are to be interpreted as described in
BCP 14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all
capitals, as shown here.

## Motivation For and Status of This Document
The original motivation of {{!RFC4180}} was to provide a reference
in order to register the media type "text/csv". It tried to document 
existing practices at the time based on the approaches used by most implementations.
This document continues to do the same, and updates the original document to reflect
current practices for generating and consuming of CSV files.
 
Both {{!RFC4180}} and this document are published as informational RFC for the benefit
of the Internet community and and not intended to be used as formal standards.
Implementers should consult {{?RFC1796}} and {{?RFC2026}} for crucial differences
between IETF standards and informational RFCs.

# Definition of the CSV Format {#format}
While there had been various specifications and implementations for the
CSV format (for ex. {{CREATIVYST}}, {{EDOCEO}}, {{CSVW}} and {{ART}})), prior to publication
of {{!RFC4180}} there is no attempt to provide a common specification. This section documents
the format that seems to be followed by most implementations (incorporating
changes since the publication of {{!RFC4180}}):

1. Each record is located on a separate line, ended by a line break (CR, LF or CRLF). For example:

   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

2. The last record in the file MUST have an ending line break. For example:

   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

3. The first record in the file MAY be an optional header
with the same format as normal records. This
header will contain names corresponding to the fields in the file
and SHOULD contain the same number of fields as the records in
the rest of the file. Implementers should be aware that some
applications may treat header values as unique.
The presence or absence of the header MAY be indicated via the
optional "header" parameter of this MIME type. For example:

   field_name_1,field_name_2,field_name_3CRLF<br/>
   aaa,bbb,cccCRLF<br/>
   zzz,yyy,xxxCRLF

4. Within each record, there MAY be one or more
fields, separated by commas. Each record SHOULD contain the same
number of fields throughout the file. Spaces are considered part
of a field and SHOULD NOT be ignored. The last field in the
record MUST NOT be followed by a comma. For example:

   aaa,bbb,cccCRLF

5. Each field MAY be enclosed in double quotes (however
some programs, do not use double quotes at all). If fields are not
enclosed with double quotes, then double quotes MUST NOT appear inside the fields.
For example:

   "aaa","bbb","ccc"CRLF<br/>
   zzz,yyy,xxxCRLF

6. Fields containing line breaks (CR, LF or CRLF), double quotes, hashes or commas
MUST be enclosed in double-quotes. For example:

   "aaa","b CRLF<br/>
   bb","ccc"CRLF<br/>
   zzz,yyy,xxxCRLF<br/>
   "#aaa",#bbb,cccCRLF

7. If double-quotes are used to enclose fields, then a double-quote
appearing inside a field MUST be escaped by preceding it with
another double quote. For example:

   "aaa","b""bb","ccc"CRLF

8. A hash sign MAY be used to mark lines that are meant to be commented lines.
A commented line can contain any whitespace or visible character until it is
terminated by a line break (CR, LF or CRLF).
A comment line MAY appear in every line of the file (before or after an
OPTIONAL header) but MUST NOT be mistaken with a subsequent line of a multi-line
field. Subsequent lines of multi-line fields can start with a hash sign and
MUST NOT interpreted as comments. For example:

    #commentCRLF<br/>
    aaa,bbb,cccCRLF<br/>
    #comment 2CRLF<br/>
    "aaa","this is CRLF<br/>
    # not a comment","ccc"CRLF<br/>
    zzz,yyy,xxxCRLF

## Default charset and line break values
Since the initial publication of {{!RFC4180}}, the default charset for "text/*" media types
has been changed to UTF-8 (as per {{!RFC6657}}). This document reflects this change and
the default charset for CSV files is now UTF-8.

Although section 4.1.1. of {{!RFC2046}} defines CRLF to denote line breaks,
implementers MAY recognize a single CR or LF as a line break (similar to section 3.1.1.3 
of {{?RFC7231}}). However, some implementations MAY use other values.

## ABNF Grammar

The ABNF grammar (as per {{!RFC5234}}) appears as follows:

~~~~~~~~~~
file = *((comment / record) linebreak)

comment = hash *comment-data

record = field *(comma field)

linebreak = CR / LF / CRLF

field = (escaped / non-escaped)

escaped = DQUOTE *(textdata / comma / hash / CR / LF / 2DQUOTE) DQUOTE

non-escaped = *textdata

comma = %x2C

hash = %x23

comment-data = WSP / VCHAR

textdata = WSP / %x21 / %x24-2B / %x2D-7E ;WSP / VCHAR without comma, hash and DQUOTE

CR = %x0D ;as per section B.1 of [RFC5234]

DQUOTE = %x22 ;as per section B.1 of [RFC5234]

LF = %x0A ;as per section B.1 of [RFC5234]

CRLF = CR LF ;as per section B.1 of [RFC5234]

HTAB = %x09 ;as per section B.1 of [RFC5234]

SP = %x20 ;as per section B.1 of [RFC5234]

WSP = SP / HTAB ;as per section B.1 of [RFC5234]

VCHAR =  %x21-7E ;as per section B.1 of [RFC5234]
~~~~~~~~~~

# Update to MIME Type Registration of text/csv {#registration}

The media type registration of "text/csv" should be updated as per specific
fields below: 

Encoding considerations:

> CSV MIME entities can consist of binary data
> as per section 4.8 of {{!RFC6838}}. Although section 4.1.1. of {{!RFC2046}} defines
> CRLF to denote line breaks, implementers MAY recognize a single CR or LF
> as a line break (similar to section 3.1.1.3 of {{!RFC7231}}).
> However, some implementations may use other values.

Published specification:

> While numerous private specifications exist for various programs
> and systems, there is no single "master" specification for this
> format. An attempt at a common definition can be found in {{!RFC4180}}
> and this document. Implementers should note that both documents are informational
> in nature and are not standards.
  
# IANA Considerations

IANA is directed to update the MIME type registration for "text/csv"
as per instructions provided in {{registration}} of this document
and include a reference to this document within the registration.

# Security Considerations

All security considerations as discussed in {{!RFC4180}} still apply.

# Acknowledgments

In addition to everyone thanked previously in {{!RFC4180}}, the author would like to thank
acknowledge the contributions of the following people to this document:
Alperen Belgic, Abed BenBrahim, Benjamin Kaduk, Damon Koach, Barry Leiba,
Oliver Siegmar, Marco Diniz Sousa and Greg Skinner.

A special thank you to L.T.S.

--- back
# Major format changes since {{!RFC4180}}
- Added a section clarifying motivation for this document and standards status
- Changing default encoding to UTF-8
- Allowing CR, LF and CRLF for line breaks
- Allowing HTAB in text data
- Mandating a line break at the end of the last line in the file
- Making records and headers optional, thus allowing for an empty file
- Adding definition of commented lines

# Note to Readers

> **Note to the RFC Editor:** Please remove this section prior
> to publication.

Development of this draft takes place on Github at: https://github.com/nightwatchcyber/rfc4180-bis

Comments can also be sent to the ART mailing list at: https://www.ietf.org/mailman/listinfo/art

Full list of changes can be viewed via the IETF document tracker:
https://tools.ietf.org/html/draft-shafranovich-rfc4180-bis
