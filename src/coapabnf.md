# CoAP-related ABNF

I have collected the ABNF of various RFCs related to CoAP and compiled the document below.
They can be tested here at Bill's parser <https://tools.ietf.org/tools/bap/abnf.cgi>

```txt
;---------
;| RFC5234 |
;---------

ALPHA          =  %x41-5A / %x61-7A   
								 ; A-Z / a-z
BIT            =  "0" / "1"
CHAR           =  %x01-7F
                                ; any 7-bit US-ASCII character,
                                ;  excluding NUL
CR             =  %x0D
                                ; carriage return
CRLF           =  CR LF
                                ; Internet standard newline
CTL            =  %x00-1F / %x7F
                                ; controls
DIGIT          =  %x30-39
                                ; 0-9
DQUOTE         =  %x22
                                ; " (Double Quote)
HEXDIG         =  DIGIT / "A" / "B" / "C" / "D" / "E" / "F"
HTAB           =  %x09
                                ; horizontal tab
LF             =  %x0A
                                ; linefeed
LWSP           =  *(WSP / CRLF WSP)
                                ; Use of this linear-white-space rule
                                ;  permits lines containing only white
                                ;  space that are no longer legal in
                                ;  mail headers and have caused
                                ;  interoperability problems in other
                                ;  contexts.
                                ; Do not use when defining mail
                                ;  headers and use with caution in
                                ;  other contexts.
OCTET          =  %x00-FF
                                ; 8 bits of data
SP             =  %x20
VCHAR          =  %x21-7E
                                ; visible (printing) characters
WSP            =  SP / HTAB
                                ; white space

;  ---------
; | RFC3986 |
;  ---------

URI           = scheme ":" hier-part [ "?" query ] [ "#" fragment ]
hier-part     = "//" authority path-abempty
                 / path-absolute
                 / path-rootless
                 / path-empty

URI-reference = URI / relative-ref

absolute-URI  = scheme ":" hier-part [ "?" query ]

relative-ref  = relative-part [ "?" query ] [ "#" fragment ]

relative-part = "//" authority path-abempty
                 / path-absolute
                 / path-noscheme
                 / path-empty

scheme        = ALPHA *( ALPHA / DIGIT / "+" / "-" / "." )

authority     = [ userinfo "@" ] host [ ":" port ]
userinfo      = *( unreserved / pct-encoded / sub-delims / ":" )
host          = IP-literal / IPv4address / reg-name
port          = *DIGIT

IP-literal    = "[" ( IPv6address / IPvFuture  ) "]"

IPvFuture     = "v" 1*HEXDIG "." 1*( unreserved / sub-delims / ":" )

IPv6address   =                            6( h16 ":" ) ls32
                 /                       "::" 5( h16 ":" ) ls32
                 / [               h16 ] "::" 4( h16 ":" ) ls32
                 / [ *1( h16 ":" ) h16 ] "::" 3( h16 ":" ) ls32
                 / [ *2( h16 ":" ) h16 ] "::" 2( h16 ":" ) ls32
                 / [ *3( h16 ":" ) h16 ] "::"    h16 ":"   ls32
                 / [ *4( h16 ":" ) h16 ] "::"              ls32
                 / [ *5( h16 ":" ) h16 ] "::"              h16
                 / [ *6( h16 ":" ) h16 ] "::"

h16           = 1*4HEXDIG
ls32          = ( h16 ":" h16 ) / IPv4address
IPv4address   = dec-octet "." dec-octet "." dec-octet "." dec-octet
dec-octet     = DIGIT                 ; 0-9
                 / %x31-39 DIGIT         ; 10-99
                 / "1" 2DIGIT            ; 100-199
                 / "2" %x30-34 DIGIT     ; 200-249
                 / "25" %x30-35          ; 250-255

reg-name      = *( unreserved / pct-encoded / sub-delims )

path          = path-abempty    ; begins with "/" or is empty
                 / path-absolute   ; begins with "/" but not "//"
                 / path-noscheme   ; begins with a non-colon segment
                 / path-rootless   ; begins with a segment
                 / path-empty      ; zero characters

path-abempty  = *( "/" segment )
path-absolute = "/" [ segment-nz *( "/" segment ) ]
path-noscheme = segment-nz-nc *( "/" segment )
path-rootless = segment-nz *( "/" segment )
path-empty    = 0<pchar>

segment       = *pchar
segment-nz    = 1*pchar
segment-nz-nc = 1*( unreserved / pct-encoded / sub-delims / "@" )
                 ; non-zero-length segment without any colon ":"

pchar         = unreserved / pct-encoded / sub-delims / ":" / "@"

query         = *( pchar / "/" / "?" )

fragment      = *( pchar / "/" / "?" )

pct-encoded   = "%" HEXDIG HEXDIG

unreserved    = ALPHA / DIGIT / "-" / "." / "_" / "~"
reserved      = gen-delims / sub-delims
gen-delims    = ":" / "/" / "?" / "#" / "[" / "]" / "@"
sub-delims    = "!" / "$" / "&" / "'" / "(" / ")"
                 / "*" / "+" / "," / ";" / "="

;  ---------
; | RFC7252 |
;  ---------

coap-URI = "coap:" "//" host [ ":" port ] path-abempty [ "?" query ]
coaps-URI = "coaps:" "//" host [ ":" port ] path-abempty [ "?" query ]


;  ---------
; | RFC6690 |
;  ---------

Link            = link-value-list
link-value-list = [ link-value *[ "," link-value ]]
link-value     = "<" URI-Reference ">" *( ";" link-param )
link-param     = ( ( "rel" "=" relation-types )
                   / ( "anchor" "=" DQUOTE URI-Reference DQUOTE )
                   / ( "rev" "=" relation-types )
                   / ( "hreflang" "=" Language-Tag )
                   / ( "media" "=" ( MediaDesc
                          / ( DQUOTE MediaDesc DQUOTE ) ) )
                   / ( "title" "=" quoted-string )
                   / ( "title*" "=" ext-value )
                   / ( "type" "=" ( media-type / quoted-mt ) )
                   / ( "rt" "=" relation-types )
                   / ( "if" "=" relation-types )
                   / ( "sz" "=" cardinal )
                   / ( link-extension ) )
link-extension = ( parmname [ "=" ( ptoken / quoted-string ) ] )
                   / ( ext-name-star "=" ext-value )
ext-name-star  = parmname "*" ; reserved for RFC-2231-profiled
                                  ; extensions.  Whitespace NOT
                                  ; allowed in between.
ptoken         = 1*ptokenchar
ptokenchar     = "!" / "#" / "$" / "%" / "&" / "'" / "("
                   / ")" / "*" / "+" / "-" / "." / "/" / DIGIT
                   / ":" / "<" / "=" / ">" / "?" / "@" / ALPHA
                   / "[" / "]" / "^" / "_" / "`" / "{" / "|"
                   / "}" / "~"
media-type     = type-name "/" subtype-name
quoted-mt      = DQUOTE media-type DQUOTE
relation-types = relation-type
                   / DQUOTE relation-type *( 1*SP relation-type ) DQUOTE
relation-type  = reg-rel-type / ext-rel-type
reg-rel-type   = LOALPHA *( LOALPHA / DIGIT / "." / "-" )
ext-rel-type   = URI
cardinal       = "0" / ( %x31-39 *DIGIT )
LOALPHA        = %x61-7A   ; a-z
;quoted-string  = <defined in [RFC2616]>
;URI            = <defined in [RFC3986]>
;URI-Reference  = <defined in [RFC3986]>
;type-name      = <defined in [RFC4288]>
;subtype-name   = <defined in [RFC4288]>
;MediaDesc      = <defined in [W3C.HTML.4.01]>
;Language-Tag   = <defined in [RFC5646]>
;ext-value      = <defined in [RFC5987]>
;parmname       = <defined in [RFC5987]>


;  ---------------------------------
; | draft-ietf-core-http-mapping-12 |
;  ---------------------------------

s  = "coap" / "coaps" ; from [RFC7252], Sections 6.1 and 6.2
hp = host [":" port]  ; from [RFC3986], Sections 3.2.2 and 3.2.3
p  = path-abempty     ; from [RFC3986], Section 3.3
q  = query            ; from [RFC3986], Section 3.4
qq = [ "?" query ]    ; qq is empty if and only if 'query' is empty
tu = coap-URI / coaps-URI  ; from [RFC7252], Section 6.1 and 6.2

```