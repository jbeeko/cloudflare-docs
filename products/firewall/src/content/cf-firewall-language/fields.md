---
order: 610
---

# Fields

The Cloudflare Firewall Rules language supports a range of field types:

- [Standard fields](#standard-fields) represent common, typically static properties of an HTTP request.
- [Dynamic fields](#dynamic-fields) represent computed or derived values, typically related to Cloudflare threat intelligence about the request.
- [URI argument and value fields](#uri-argument-and-value-fields) are extracted from the request.
- [HTTP header fields](#http-header-fields) represent the names and values associated with HTTP request headers.
- [HTTP body fields](#http-body-fields) represent the properties of an HTTP request body, including forms, for example.

## Standard fields

Most standard fields use the same naming conventions as [Wireshark display fields](https://www.wireshark.org/docs/wsug_html_chunked/ChWorkBuildDisplayFilterSection.html). However, there are some subtle differences between Cloudflare and Wireshark:

- Wireshark supports [CIDR (Classless Inter-Domain Routing) notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) for expressing IP address ranges in equality comparisons (`ip.src == 1.2.3.0/24`, for example). Cloudflare does not.

  To evaluate a range of addresses using CIDR notation, use the `in` [comparison operator](/cf-firewall-language/operators/#comparison-operators) as in this example: `ip.src in {1.2.3.0/24 4.5.6.0/24}`.

- In Wireshark, `ssl` is a protocol field containing hundreds of other fields of various types that are available for comparison in multiple ways. However, in Firewall Rules `ssl` is a single Boolean field that indicates whether the connection from the client to Cloudflare is encrypted.

- The Cloudflare Firewall Rules language does not support the `slice` operator.

The Cloudflare Firewall Rules language supports these standard fields:

<TableWrap>
<table style="width: 100%;">
  <thead>
    <tr>
      <td><strong>Field Name</strong></td>
      <td><strong>Type</strong></td>
      <td><strong>Description</strong></td>
    </tr>
  </thead>
  <tbody>
   <tr>
      <td valign="top"><code>http.cookie</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the entire cookie as a string.</p>
         <p>Example value:
         <br /><code>session=8521F670545D7865F79C3D7BEDC29CCE;-background=light</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.host</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the host name used in the full request URI.
         </p>
         <p>Example value:
         <br /><code>www.example.org</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.referer</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the HTTP Referer request header, which contains the address of the web page that linked to the currently requested page.
         </p>
         <p>Example value:
         <br /><code>Referer: htt&shy;ps://developer.example.org/en-US/docs/Web/JavaScript</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.full_uri</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the full URI as received by the web server (does not include <code>#fragment</code>, which is not sent to web servers).
         </p>
         <p>Example value:
         <br /><code>htt­ps://www.example.org/articles/index?section=539061&expand=comments</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.method</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the HTTP method, returned as a string of uppercase characters.
         </p>
         <p>Example value:
         <br /><code>GET</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.timestamp.sec</code></td>
      <td valign="top">Integer</td>
      <td>
         <p>Represents the timestamp when Cloudflare received the request, expressed as Unix time in seconds. This value is 10 digits long.
         </p>
         <p>Example value:
         <br /><code>1484063137</code>
         </p>
         <p>When validating HMAC tokens in an expression, pass this field as the <em>currentTimestamp</em> argument to the <code>is_timed_hmac_valid_v()</code> <a href="/firewall/cf-firewall-language/functions/#hmac-validation">validation function</a>.
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.uri</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the absolute URI of the request.
         </p>
         <p>Example value:
         <br /><code>/articles/index?section=539061&expand=comments</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.uri.path</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the URI path of the request.
         </p>
         <p>Example value:
         <br /><code>/articles/index</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.uri.query</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the entire query string, without the <code>?</code> delimiter.
         </p>
         <p>Example value:
         <br /><code>section=539061&expand=comments</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.user_agent</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the HTTP user agent, a request header that contains a characteristic string to allow identification of the client operating system and web browser.
         </p>
         <p>Example value:
         <br /><code>Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.version</code></td>
      <td valign="top">Number</td>
      <td>
         <p>Represents the version of the HTTP protocol used. Use this field when you require different checks for different versions.
         </p>
         <p>Example Values:
            <ul>
               <li><code>HTTP/1.1</code></li>
               <li><code>HTTP/3</code></li>
            </ul>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.x_forwarded_for</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the full <code>X-Forwarded-For</code> HTTP header.
         </p>
         <p>Example value:
         <br /><code>203.0.113.195, 70.41.3.18</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>ip.src</code></td>
      <td valign="top">IP&nbsp;address</td>
      <td>
         <p> Represents the client TCP IP address, which may be adjusted to reflect the actual address of the client by using, for example, HTTP headers such as
         <code>X-Forwarded-For</code> or <code>X-Real-IP</code>.
         </p>
         <p>Example value:
         <br /><code>93.184.216.34</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>ip.geoip.asnum</code></td>
      <td valign="top">Number</td>
      <td>
         <p>Represents the 16- or 32-bit integer representing the Autonomous System (AS) number associated with client IP address.
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>ip.geoip.continent</code></td>
      <td valign="top">String</td>
      <td>
         Represents the continent code associated with client IP address:
          <ul>
              <li>AF &#8211; Africa</li>
              <li>AN &#8211; Antarctica</li>
              <li>AS &#8211; Asia</li>
              <li>EU &#8211; Europe</li>
              <li>NA &#8211; North America</li>
              <li>OC &#8211; Oceania</li>
              <li>SA &#8211; South America</li>
              <li>T1 &#8211; Tor network</li>
          </ul>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>ip.geoip.country</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the 2-letter country code in <a href="https://www.iso.org/obp/ui/#search/code/">ISO 3166-1 Alpha 2</a> format.
         </p>
         <p>Example value:
         <br /><code>GB</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>ip.geoip.subdivision_1_iso_code</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the ISO 3166-2 code for the first level region associated with the IP address. If the actual value is not available, this field contains an empty string.</p>
         <p>Example value:
         <br />
         <code>GB-ENG</code></p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>ip.geoip.subdivision_2_iso_code</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the ISO 3166-2 code for the second level region associated with the IP address. If the actual value is not available, this field contains an empty string.
         </p>
         <p>Example value:
         <br />
         <code>GB-SWK</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>ip.geoip.is_in_european_union</code></td>
      <td valign="top">Boolean</td>
      <td>
         <p>Returns <code>true</code> when the request originates from an EU country.
         </p>
      </td>
  </tr>
  <tr>
      <td valign="top"><code>ssl</code></td>
      <td valign="top">Boolean</td>
      <td>
         <p>Returns<code>true</code> when the HTTP connection to the client is encrypted.
         </p>
      </td>
   </tr>
  </tbody>
</table>
</TableWrap>

## Dynamic fields

Dynamic fields represent computed or derived values, typically related to threat intelligence about an HTTP request.

<Aside type='warning'>

Access to the `cf.bot_management.verified_bot` field requires a Cloudflare Enterprise plan.

</Aside>

The Cloudflare Firewall Rules language supports these dynamic fields:

<TableWrap>
<table style="width: 100%;">
  <thead>
   <tr>
      <th>Field Name</th>
      <th>Type</th>
      <th>Description</th>
   </tr>
  </thead>
  <tbody style='vertical-align:top'>
    <tr>
        <td><p><code>cf.bot_management.verified_bot</code></p>
        </td>
        <td>Boolean</td>
        <td>
          <p>When <code>true</code>, this field indicates the request originated from a known bot or crawler. It does not identify good or bad intent.
          </p>
        </td>
    </tr>
    <tr>
        <td><code>cf.client.bot</code></td>
        <td>Boolean</td>
        <td>
          <p>When <code>true</code>, this field indicates the client of a request is a known good bot.</p>
        </td>
    </tr>
    <tr>
        <td><code>cf.edge.server_port</code></td>
        <td>Number</td>
        <td>
          <p>Represents the port number at which Cloudflare's network received the request.
          </p>
          <p>Use this field to filter traffic on a specific port. The value is a port number in the range 1–65535.</p>
        </td>
    </tr>
    <tr>
        <td><code>cf.threat_score</code></td>
        <td>Number</td>
        <td>
          <p>Represents a Cloudflare threat score from 0&#8211;100, where 0 indicates low risk. Values above 10 may represent spammers or bots, and values above 40 identify bad actors on the internet.
          </p>
          <p>It is rare to see values above 60. A common recommendation is to challenge requests with a score above 10 and to block those above 50.
          </p>
        </td>
    </tr>
    <tr>
      <td><code>cf.tls_client_auth.cert_verified</code></td>
      <td>Boolean</td>
      <td>Returns <code>true</code> when a request presents a valid client certificate.</td>
    </tr>
    <tr>
      <td><code>cf.worker.upstream_zone</code></td>
      <td>String</td>
      <td>
        <p>Identifies whether a request comes from a worker or not.</p>
        <p>When a request comes from a worker, this field will hold the name of the zone for that worker. Otherwise <code>cf.worker.upstream_zone</code> is empty</p>
      </td>
    </tr>
  </tbody>
</table>
</TableWrap>

## URI argument and value fields

The Cloudflare Firewall Rules language includes URI argument and value fields associated with HTTP requests. Many of these fields return arrays containing the respective values. Those that do require the `[*]` index operator, which returns an array of values for each field.

The Cloudflare Firewall Rules language supports these URI argument and value fields:

<TableWrap>
<table style="width: 100%;">
  <thead>
    <tr>
      <td><strong>Field Name</strong></td>
      <td><strong>Type</strong></td>
      <td><strong>Description</strong></td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top"><code>http.request.uri.args</code></td>
      <td valign="top">Map &lt;String&gt;&lt;Array&gt;</td>
      <td>
        <p>Represents the HTTP URI arguments associated with a request as a Map (associative array).
        </p>
        <p>If an argument is repeated, then the array will contain multiple items in the order they appear in the request.
        </p>
        <p>Values are not pre-processed and retain the case used in the request.</p>
        <p><em>Decoding:</em> no decoding performed
        <br /><em>Non-ASCII:</em> preserved
        </p>
        <p>Example:
        <br /><code>any(http.request.uri.args["search"][*] == "red+apples")</code>
        </p>
        <p>Example value:
        <br /><code>{'{"search": ["red+apples"]}'}</code>
        </p>
      </td>
    </tr>
    <tr>
      <td valign="top"><code>http.request.uri.args.names</code></td>
      <td valign="top">Array&lt;String></td>
      <td>
        <p>Represents the names of the arguments in the HTTP URI query string. Names are not pre-processed and retain the case used in the request.
        </p>
        <p>If a name repeats, the array contains multiple items in the order that they appear in the request.
        </p>
        <p><em>Decoding:</em> no decoding performed
        <br /><em>Non-ASCII:</em> preserved
        </p>
        <p>Example:
        <br /><code>any(http.request.uri.args.names[*] == "search")</code>
        </p>
        <p>Example value:
        <br /><code>["search"]</code>
        </p>
      </td>
    </tr>
    <tr>
      <td valign="top"><code>http.request.uri.args.values</code></td>
      <td valign="top">Array&lt;String></td>
      <td>
        <p>Represents the values of arguments in the HTTP URI query string. Values are not pre-processed and retain the case used in the request. They are in the same order as in the request.
        </p>
        <p>Duplicated values are listed multiple times.
        </p>
        <p><em>Decoding:</em> no decoding performed
        <br /><em>Non-ASCII:</em> preserved
        </p>
        <p>Example:
        <br /><code>any(http.request.uri.args.values[*] == "red+apples")</code>
        </p>
        <p>Example value:
        <br /><code>["red+apples"]</code>
        </p>
      </td>
    </tr>
  </tbody>
</table>
</TableWrap>

## HTTP header fields

The Firewall Rules language includes fields that represent properties of HTTP request headers. Many of these return arrays containing the respective values. Those that do require the `[*]` index operator, which returns an array of values for each field.

The Cloudflare Firewall Rules language supports these HTTP header fields:

<TableWrap>
<table style="width: 100%;">
   <tr>
      <td><strong>Field Name</strong></td>
      <td><strong>Type</strong></td>
      <td><strong>Description</strong></td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.headers</code></td>
      <td valign="top">Map&lt;String&gt;&lt;Array&gt;</td>
      <td>
         <p>Represents HTTP request headers as a Map (or associative array).
         </p>
         <p>When there are repeating headers, the array includes them in the order they appear in the request. The keys will be converted to lowercase.</p>
         <p><em><em>Decoding:</em></em> no decoding performed
         <br /><em>Whitespace:</em> preserved
         <br /><em>Non-ASCII:</em> preserved
         </p>
         <p>Example:
         <br /><code>any(http.request.headers["content-type"][*] == "application/json")</code>
         </p>
         <p>Example value:
         <br /><code>{'{"content-type": ["application/json"]}'}</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.header.names</code></td>
      <td valign="top">Array&lt;String></td>
      <td>
         <p>Represents the names of the headers in the HTTP request. The names are not pre-processed and retain the case used in the request.
         </p>
         <p>The order of header names is not guaranteed but will match <code>http.request.headers.values</code>.
         </p>
         <p>Duplicate headers are listed multiple times.
         </p>
         <p><em>Decoding:</em> no decoding performed
         <br /><em>Whitespace:</em> preserved
         <br /><em>Non-ASCII:</em> preserved
         </p>
         <p>Example:
         <br /><code>any(http.request.headers.names[*] == "content-type")</code>
         </p>
         <p>Example value:
         <code>["content-type"]</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.header.values</code></td>
      <td valign="top">Array&lt;String></td>
      <td>
         <p>Represents the values of the headers in the HTTP request.</p>
         <p>Values are not pre-processed and retain the case used in the request.</p>
         <p>The order of header values is not guaranteed but will match <code>http.request.headers.names</code>.
         </p>
         <p>Duplicate headers are listed multiple times.
         </p>
         <p><em>Decoding:</em> no decoding performed
         <br /><em>Whitespace:</em> preserved
         <br /><em>Non-ASCII:</em> preserved
         </p>
         <p>Example:
         <br />
         <code>any(http.request.headers.values[*] == "application/json")</code>
         </p>
         <p>Example value:
         <br />
         <code>["application/json"]</code>
         </p>
      </td>
   </tr>
   <tr>
      <td valign="top"><code>http.request.header.truncated</code></td>
      <td valign="top">Boolean</td>
      <td>
         <p>Returns <code>true</code> when the HTTP request contains too many headers; otherwise, returns <code>false</code>.
         </p>
         <p>When <code>true,</code> <code>http.request.headers</code>, <code>http.request.headers.names</code>, and <code>http.request.headers.values</code> may not contain all of the headers sent in the HTTP request.
         </p>
      </td>
   </tr>
</table>
</TableWrap>

## HTTP body fields

<Aside type='warning'>

Access to HTTP body fields requires a Cloudflare Enterprise plan.

</Aside>

The Firewall Rules language includes fields that represent properties of an HTTP request body. Many of these return arrays containing the respective values. Those that do require the `[*]` index operator, which returns an array of values for each field.

The Cloudflare Firewall Rules language supports these HTTP body fields:

<TableWrap>
<table style="width: 100%;">
  <thead>
    <tr>
      <td><strong>Field Name</strong></td>
      <td><strong>Type</strong></td>
      <td><strong>Description</strong>
      </td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td valign="top"><code>http.request.body.raw</code></td>
      <td valign="top">String</td>
      <td>
         <p>Represents the unaltered HTTP request body.
         </p>
         <p>When the value of <code>http.request.body.truncated</code> is <code>true</code>, the return value may be truncated.
         </p>
         <p><em>Decoding:</em> no decoding performed
         <br /><em>Whitespace:</em> preserved
         <br /><em>Non-ASCII:</em> preserved
         </p>
      </td>
    </tr>
    <tr>
      <td valign="top"><code>http.request.body.truncated</code></td>
      <td valign="top">Boolean</td>
      <td>
         <p>Indicates whether the HTTP request body is truncated.
         </p>
         <p>When true, <code>http.request.body</code> fields may not contain all of the HTTP request body.
         </p>
      </td>
    </tr>
    <tr>
      <td valign="top"><code>http.request.body.form</code></td>
      <td valign="top">Map&lt;String&gt;&lt;Array&gt;</td>
      <td>
         <p>Represents the HTTP request body of a form as a Map (or associative array). Populated when the <code>Content-Type</code> header is <code>application/x-www-form-urlencoded</code>.
         </p>
         <p>Values are not pre-processed and retain the case used in the request. If a field repeats, then the array contains multiple items in the order they are in the request.
         </p>
         <p>The return value may be truncated if <code>http.request.body.truncated</code> is <code>true</code>.
         </p>
         <p><em>Decoding:</em> no decoding performed
         <br /><em>Whitespace:</em> preserved
         <br /><em>Non-ASCII:</em> preserved
         </p>
         <p>Example:
         <br /><code>any(http.request.body.form["username"][*] == "admin")</code>
         </p>
         <p>Example value:
         <br /><code>{'{username": ["admin"]}'}</code>
         </p>
      </td>
    </tr>
    <tr>
      <td valign="top"><code>http.request.body.form.names</code></td>
      <td valign="top">Array&lt;String></td>
      <td>
         <p>Represents the names of the form fields in an HTTP request where the content type is <code>application/x-www-form-urlencoded</code>.
         </p>
         <p>Names are not pre-processed and retain the case found in the request. They are listed in the same order as in the request. Duplicate names are listed multiple times.
         </p>
         <p>The return value may be truncated if <code>http.request.body.truncated</code> is <code>true</code>.
         </p>
         <p><em>Decoding:</em> no decoding performed
         <br /><em>Whitespace:</em> preserved
         <br /><em>Non-ASCII:</em> preserved
         </p>
         <p>Example:
         <br /><code>any(http.request.body.form.names[*] == "username")</code>
         </p>
         <p>Example value:
         <br /><code>["username"]</code>
         </p>
      </td>
    </tr>
    <tr>
      <td valign="top"><code>http.request.body.form.values</code></td>
      <td valign="top">Array&lt;String></td>
      <td>
         <p>Represents the values of the form fields in an HTTP request where the content type is <code>application/x-www-form-urlencoded</code>.
         </p>
         <p>Values are not pre-processed and retain the case used in the request. They are in the same order as in the request.
         </p>
         <p>Duplicated values are listed multiple times.
         </p>
         <p>The return value may be truncated if  <code>http.request.body.truncated</code> is <code>true</code>.
         </p>
         <p><em>Decoding:</em> no decoding performed
         <br /><em>Whitespace:</em> preserved
         <br /><em>Non-ASCII:</em> preserved
         </p>
         <p>Example:
         <br /><code>any(http.request.body.form.values[*] == "admin")</code>
         </p>
         <p>Example value:
         <br /><code>["admin"]</code>
         </p>
      </td>
    </tr>
  </tbody>
</table>
</TableWrap>
