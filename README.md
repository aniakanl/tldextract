# TLDExtractor [![Codacy Badge](https://api.codacy.com/project/badge/Grade/cb49a4752ea24f16968f46624cd7c5de)](https://www.codacy.com/app/aniakanl/TLDExtractor?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=aniakanl/TLDExtractor&amp;utm_campaign=Badge_Grade)

## C# Library
`TLDExtractor` is similar to [tldextract library (in Python)](https://github.com/john-kurkowski/tldextract). By using TLDExtractor, you can accurately extract subdomain, domain, and domain suffix (effective TLD) of a given domain name.

It is a common mistake to split a domain name with '.' character and consider the last part as a domain suffix. For example, consider 'www.yahoo.co.uk', in this domain, 'co.uk' is the suffix not 'uk'.

`TLDExtractor` utilize [the Public Suffix List](https://www.publicsuffix.org/list/public_suffix_list.dat) to correctly identify the suffix for a given domain name. To extract subdomain, domain, and domain suffix, you can use `TLDExtractor.Extract` methods:

```csharp
var result = TLDExtractor.Extract("www.yahoo.co.uk");
// result.ToString() -> 
//    {ExtractResult(subdomain='www', domain='yahoo', suffix='co.uk', suffix type='ICANN')}

// nom.ae is a private suffix submitted by Dave McCormack <dave.mccormack@nymnom.com>
var result = TLDExtractor.Extract("www.test.nom.ae");
// result.ToString() -> 
//    {ExtractResult(subdomain='www', domain='test', suffix='nom.ae', suffix type='Private')}

// you can pass a Uri
Uri guestUrl = new Uri("http://www.example.com/mine.html");
var result = TLDExtractor.Extract(guestUrl);
// result.ToString() -> 
//    {ExtractResult(subdomain='www', domain='example', suffix='com', suffix type='ICANN')}
```

If the domain name is not valid, `TLDExtractor.Extract` raises `TLDExtractorException` with an appropriate message explaining the problem. According to [RFC 1035](https://tools.ietf.org/html/rfc1035), domain names must be equal or less than 255 characters, and each label such as `yahoo` or `www` must be equal or less than 63 characters. Moreover, domain labels cannot be empty. For example, test..com is not valid. 

In addition to `TLDExtractor.Extract` method, you can use `TLDExtractor.TryExtract` method to extract domain parts from a domain or URL, the only difference these two is that `TLDExtractor.TryExtract` does not raise any `TLDExtractorException` exception. It returns false if the given domain name is not a valid one.

```csharp
ExtractResult result;
bool isExtracted = TLDExtractor.TryExtract("www.yahoo.co.uk", out result);

if(isExtracted)
{
    // do something
}
else
{
    // something went wrong
}
```

By default, `TLDExtractor` downloads [the Public Suffix List](https://www.publicsuffix.org/list/public_suffix_list.dat) from the Internet if it doesn't exist in the working directory or it is too old (it is created more than 30 days). You can override these default values by

```csharp
TLDExtractor.SuffixListFilePath = "change_the_default_file_name.txt";

TLDExtractor.RenewAfterNDays = 60; 

TLDExtractor.RenewAfterNDays = -1; // to disable renewal mechanism
```
