![image](https://www.lucas-cueff.com/files/gallery.png)
# Use-WhoisOvertHTTP
Simple PowerShell commandline interface to use Whois RIPE Database REST API

Use-WhoisOverHTTP.psm1 module provides a commandline interface to RIPE Database REST API

Please consult Wiki available on Ripe-NCC github :
https://github.com/RIPE-NCC/whois/wiki/WHOIS-REST-API
Please consult WhoIs query reference maunal :
https://www.ripe.net/manage-ips-and-asns/db/support/documentation/ripe-database-query-reference-manual

Geolocation rest API is not implemented because this API is deprecated
https://github.com/RIPE-NCC/whois/wiki/WHOIS-REST-API-geolocation

(c) 2018-2019 lucas-cueff.com Distributed under Artistic Licence 2.0 (https://opensource.org/licenses/artistic-license-2.0).

## install Use-WhoisOvertHTTP from PowerShell Gallery repository
You can easily install it from powershell gallery repository
https://www.powershellgallery.com/packages/Use-WhoisOvertHTTP/
using a simple powershell command and an internet access :-) 
```
	Install-Module -Name Use-WhoisOvertHTTP
```
## import module from PowerShell 
```	
	.EXAMPLE
	C:\PS> import-module Use-WhoisOvertHTTP.psd1
  
```
## module content
###  Get-Whois function
```
  .SYNOPSIS 
    A Powershell function offering the well-known whois search via ripe whois rest-like interface.
    HTTP Status Codes :
    code	description
    200		Search successfull
    400		Illegal input - incorrect value in one or more of the parameters
    404		No object(s) found

    .DESCRIPTION
    Please consult Wiki available on Ripe-NCC github :
    https://github.com/RIPE-NCC/whois/wiki/WHOIS-REST-API-search
    Please consult WhoIs query reference maunal :
	https://www.ripe.net/manage-ips-and-asns/db/support/documentation/ripe-database-query-reference-manual
	
	.PARAMETER SearchInfo
	-SearchInfo string
    Mandadatory
    Search term, could be : ip, as number, org...

    .PARAMETER Sources
    -Sources string or array of the following strings : 'all','ripe','afrinic-grs','apnic-grs','arin-grs','jpirr-grs','lacnic-grs','radb-grs','ripe-grs'
    Mandatory
    Dynamic parameter validation based on Get-WhoisSources output
    Set the source to be used for whois. 'all' will use all the available sources.
    Use Update-WhoisInfoAsEnvVariable function to put available sources in cache using a global env variable and avoid unnecessary online request.

    .PARAMETER TypeFilter
    -TypeFilter string or array of strings : 'inet6num','route6','inetnum','route','domain','inet-rtr','mntner','organisation','person','role'
    Optional
    Dynamic parameter validation based on Get-WhoisObjectFilterFromString output
    If specified the results will be filtered by object-type, multiple type-filters can be specified. The filter available depends on the search term.

    .PARAMETER ReverseLookup
    -ReverseLookup string : 'abuse-c','abuse-mailbox','admin-c','auth','author','ds-rdata','fingerpr','form','ifaddr','irt-nfy','local-as','mbrs-by-ref','member-of','mnt-by','mnt-domains','mnt-irt','mnt-lower','mnt-nfy','mnt-ref','mnt-routes','notify','nserver','org','origin','ping-hdl','ref-nfy','tech-c','upd-to','zone-c'
    Optional
    Dynamic parameter validation based on Get-WhoisAllObjectsInverseKey output
    If used, the query is an inverse lookup on the given attribute, if not specified the query is a direct lookup search.
    Use Update-WhoisInfoAsEnvVariable function to put available sources in cache using a global env variable and avoid unnecessary online request.
    
    .PARAMETER Flags
    -Flags string or array of strings :'show-personal','no-personal','no-filtering','no-referenced','no-irt'
    Optional
    'show-personal' : Include referenced person and role objects in results.
    'no-personal' : Filter referenced person and role objects from results. A client can be blocked for excessive querying of these objects.
    'no-filtering' : Switches off default filtering of objects.
    'no-referenced' : Switches off lookups for referenced contact information after retrieving the objects that match the lookup key.
    'no-irt' : Filter IRT object (An irt object represents a Computer Security Incident Response Team (CSIRT))

    .PARAMETER LimitResult
    -LimitResult string {number}
    Optional
    Limit result object number - Set a max object number to be get from the server

    .PARAMETER SimpleOutput 
    -SimpleOutput {Switch}
    Optional
    filter the powershell object sent back from cdmlet to show attributes property

    .PARAMETER NoTLS
    -NoTLS {Switch}
    Optional
    Don't use TLS to communicate with RIPE server (HTTP only)
    
	.OUTPUTS
	   TypeName : System.Management.Automation.PSCustomObject

        Name                 MemberType   Definition
        ----                 ----------   ----------
        Equals               Method       bool Equals(System.Object obj)
        GetHashCode          Method       int GetHashCode()
        GetType              Method       type GetType()
        ToString             Method       string ToString()
        objects              NoteProperty System.Management.Automation.PSCustomObject objects=@{object=System.Object[]}
        parameters           NoteProperty System.Management.Automation.PSCustomObject parameters=@{inverse-lookup=; type-fil...
        service              NoteProperty System.Management.Automation.PSCustomObject service=@{name=search}
        terms-and-conditions NoteProperty System.Management.Automation.PSCustomObject terms-and-conditions=@{type=locator; h...
        
        Name        MemberType   Definition
        ----        ----------   ----------
        Equals      Method       bool Equals(System.Object obj)
        GetHashCode Method       int GetHashCode()
        GetType     Method       type GetType()
        ToString    Method       string ToString()
        attributes  NoteProperty System.Management.Automation.PSCustomObject attributes=@{attribute=System.Object[]}
        link        NoteProperty System.Management.Automation.PSCustomObject link=@{type=locator; href=http://rest.db.ripe.n...
        primary-key NoteProperty System.Management.Automation.PSCustomObject primary-key=@{attribute=System.Object[]}
        source      NoteProperty System.Management.Automation.PSCustomObject source=@{id=apnic-grs}
    
        
    .EXAMPLE
    Valid inverse lookup query on an org value, filtering by inetnum:
    C:\PS> Get-Whois -SearchInfo ORG-NCC1-RIPE -TypeFilter inetnum -ReverseLookup org -Sources ripe

    .EXAMPLE
    Search for objects of type organisation on the same query-string and specifying a preference for non recursion:
    C:\PS> Get-Whois -SearchInfo ORG-NCC1-RIPE -TypeFilter inetnum -Flags no-referenced -ReverseLookup org -Sources ripe

    .EXAMPLE
    A search on multiple sources and flags:
    C:\PS> get-whois -SearchInfo MAINT-APNIC-AP -Flags @('no-referenced','no-irt') -Sources all

    .EXAMPLE
    A search on multiple sources and multiple type-filters:
    C:\PS> Get-Whois -SearchInfo google -Sources all -TypeFilter @('person','organisation')

    .EXAMPLE
    A search on multiple sources and multiple type-filters and limit results to 5 entries:
    C:\PS> Get-Whois -SearchInfo google -Sources all -TypeFilter @('person','organisation') -LimiResult 5

    .EXAMPLE
    A search on multiple sources and multiple type-filters and force communication to web service to HTTP only
    C:\PS> Get-Whois -SearchInfo google -Sources all -TypeFilter @('person','organisation') -NoTLS

    .EXAMPLE
    A search on multiple sources and multiple type-filters with a simplify output:
    C:\PS> Get-Whois -SearchInfo google -Sources all -TypeFilter @('person','organisation') -SimpleOutput
```
###  Get-WhoisSources function
```
	.SYNOPSIS 
    A Powershell function requesting WHOIS REST API Metadata to list available Whois sources (A WhoisResource containing all available sources)
    HTTP Status Codes :
    code	description
    200		Search successfull

    .DESCRIPTION
    Please consult Wiki available on Ripe-NCC github :
    https://github.com/RIPE-NCC/whois/wiki/WHOIS-REST-API-metadata#listsources
    Please consult WhoIs query reference maunal :
	https://www.ripe.net/manage-ips-and-asns/db/support/documentation/ripe-database-query-reference-manual
	
    .PARAMETER SimpleOutput 
    -SimpleOutput {Switch}
    Optional
    filter the powershell object sent back from cdmlet to show attributes property

    .PARAMETER NoTLS
    -NoTLS {Switch}
    Optional
    Don't use TLS to communicate with RIPE server (HTTP only)
    
	.OUTPUTS
        TypeName : System.Management.Automation.PSCustomObject

    Name        MemberType   Definition
    ----        ----------   ----------
    Equals      Method       bool Equals(System.Object obj)
    GetHashCode Method       int GetHashCode()
    GetType     Method       type GetType()
    ToString    Method       string ToString()
    link        NoteProperty System.Management.Automation.PSCustomObject link=@{type=locator; href=http://rest.db.ripe.net/metadata/sources}
    service     NoteProperty System.Management.Automation.PSCustomObject service=@{name=getSupportedDataSources}
    sources     NoteProperty System.Management.Automation.PSCustomObject sources=@{source=System.Object[]}

        TypeName : System.String

    Name             MemberType            Definition
    ----             ----------            ----------
    Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
    CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), int IComparable.CompareTo(System.Object obj), int IComparable[string].CompareTo(string other)
    Contains         Method                bool Contains(string value)
    CopyTo           Method                void CopyTo(int sourceIndex, char[] destination, int destinationIndex, int count)
    EndsWith         Method                bool EndsWith(string value), bool EndsWith(string value, System.StringComparison comparisonType), bool EndsWith(string value, bool ignoreCase, cultureinfo culture)
    Equals           Method                bool Equals(System.Object obj), bool Equals(string value), bool Equals(string value, System.StringComparison comparisonType), bool IEquatable[string].Equals(string other)
    GetEnumerator    Method                System.CharEnumerator GetEnumerator(), System.Collections.IEnumerator IEnumerable.GetEnumerator(), System.Collections.Generic.IEnumerator[char] IEnumerable[char].GetEnumerator()
    GetHashCode      Method                int GetHashCode()
    GetType          Method                type GetType()
    ...
       
    .EXAMPLE
    Get available Whois sources
    C:\PS> Get-WhoisSources

    .EXAMPLE
    Get available Whois sources and force communication to web service to HTTP only
    C:\PS> Get-WhoisSources -NoTLS

    .EXAMPLE
    Get available Whois sources and force communication to web service to HTTP only with a simplify output:
    C:\PS> Get-WhoisSources -SimpleOutput -NoTLS
```
###  Get-WhoisObjectTemplateInfo function
 ```
 	.SYNOPSIS 
    A Powershell function requesting WHOIS REST API Metadata to list available attributes for Whois template object (A WhoisResource containing the template of the specified type)
    HTTP Status Codes :
    code	description
    200		Search successful
    400	    Illegal input - incorrect objectType

    .DESCRIPTION
    Please consult Wiki available on Ripe-NCC github :
    https://github.com/RIPE-NCC/whois/wiki/WHOIS-REST-API-metadata#objecttemplate
    Please consult WhoIs query reference maunal :
	https://www.ripe.net/manage-ips-and-asns/db/support/documentation/ripe-database-query-reference-manual
	
	.PARAMETER WhoisObject
	-WhoisObject string from the following list : 'as-block','as-set','aut-num','domain','filter-set','inet6num','inetnum','inet-rtr','irt','key-cert','mntner','organisation','peering-set','person','poem','poetic-form','role','route','route6','route-set','rtr-set'
    Mandadatory

    .PARAMETER SimpleOutput 
    -SimpleOutput {Switch}
    Optional
    filter the powershell object sent back from cdmlet to show attributes property

    .PARAMETER NoTLS
    -NoTLS {Switch}
    Optional
    Don't use TLS to communicate with RIPE server (HTTP only)
    
	.OUTPUTS
	   TypeName : System.Management.Automation.PSCustomObject

        Name        MemberType   Definition
        ----        ----------   ----------
        Equals      Method       bool Equals(System.Object obj)
        GetHashCode Method       int GetHashCode()
        GetType     Method       type GetType()
        ToString    Method       string ToString()
        link        NoteProperty System.Management.Automation.PSCustomObject link=@{type=locator; href=http://rest.db.ripe.net/metadata/templates/as-block}
        service     NoteProperty System.Management.Automation.PSCustomObject service=@{name=getObjectTemplate}
        templates   NoteProperty System.Management.Automation.PSCustomObject templates=@{template=System.Object[]}
        
        Name        MemberType   Definition
        ----        ----------   ----------
        Equals      Method       bool Equals(System.Object obj)
        GetHashCode Method       int GetHashCode()
        GetType     Method       type GetType()
        ToString    Method       string ToString()
        cardinality NoteProperty string cardinality=SINGLE
        keys        NoteProperty Object[] keys=System.Object[]
        name        NoteProperty string name=as-block
        requirement NoteProperty string requirement=MANDATORY
    
    .EXAMPLE
    C:\PS> Get-WhoisObjectTemplateInfo -WhoisObject as-block

    .EXAMPLE
    A search on multiple sources and multiple type-filters and force communication to web service to HTTP only
    C:\PS> Get-WhoisObjectTemplateInfo -WhoisObject as-block -NoTLS

    .EXAMPLE
    A search on multiple sources and multiple type-filters with a simplify output:
    C:\PS> Get-WhoisObjectTemplateInfo -WhoisObject as-block -SimpleOutput
 ```
 ###  Get-WhoisAbuseContact function
 ```
 	.SYNOPSIS 
    A Powershell function taht you can use to lookup abuse contact email for an internet resource (IPv4 address, range or prefix, IPv6 address or prefix, AS number) using RIPE WHOIS REST Abuse API.
    HTTP Status Codes :
    code	description
    200		Search successful
    404		No object(s) found

    .DESCRIPTION
    Please consult Wiki available on Ripe-NCC github :
    https://github.com/RIPE-NCC/whois/wiki/WHOIS-REST-API-abuse-contact
    Please consult WhoIs query reference maunal :
	https://www.ripe.net/manage-ips-and-asns/db/support/documentation/ripe-database-query-reference-manual
	
	.PARAMETER AbuseSearchValue
	-AbuseSearchValue string
    Mandadatory
    Search term, could be : ip, as number

    .PARAMETER SimpleOutput 
    -SimpleOutput {Switch}
    Optional
    filter the powershell object sent back from cdmlet to show attributes property

    .PARAMETER NoTLS
    -NoTLS {Switch}
    Optional
    Don't use TLS to communicate with RIPE server (HTTP only)
    
	.OUTPUTS
    TypeName : System.Management.Automation.PSCustomObject

        Name                 MemberType   Definition
        ----                 ----------   ----------
        Equals               Method       bool Equals(System.Object obj)
        GetHashCode          Method       int GetHashCode()
        GetType              Method       type GetType()
        ToString             Method       string ToString()
        abuse-contacts       NoteProperty System.Management.Automation.PSCustomObject abuse-contacts=@{key=OPS4-RIPE; email=abuse@ripe.net}
        link                 NoteProperty System.Management.Automation.PSCustomObject link=@{type=locator; href=http://rest.db.ripe.net/abuse-contact/AS3333}
        parameters           NoteProperty System.Management.Automation.PSCustomObject parameters=@{primary-key=}
        service              NoteProperty string service=abuse-contact
        terms-and-conditions NoteProperty System.Management.Automation.PSCustomObject terms-and-conditions=@{type=locator; href=http://www.ripe.net/db/support/db-terms-conditions.pdf}

        Name        MemberType   Definition
        ----        ----------   ----------
        Equals      Method       bool Equals(System.Object obj)
        GetHashCode Method       int GetHashCode()
        GetType     Method       type GetType()
        ToString    Method       string ToString()
        email       NoteProperty string email=abuse@ripe.net
        key         NoteProperty string key=OPS4-RIPE
    
        
    .EXAMPLE
    Lookup abuse contact email for AS number AS3333
    C:\PS> Get-WhoisAbuseContact AS3333

    .EXAMPLE
    Lookup abuse contact email for IP 89.89.115.192
    C:\PS> Get-WhoisAbuseContact 89.89.115.192

    .EXAMPLE
    Lookup abuse contact email for AS number AS3333 and force communication to web service to HTTP only
    C:\PS> Get-WhoisAbuseContact AS3333 -SimpleOutput -NoTLS

    .EXAMPLE
    Lookup abuse contact email for AS number AS3333 with a simplify output:
    C:\PS> Get-WhoisAbuseContact AS3333 -SimpleOutput
 ```
 ###  Invoke-WhoisOverHTTP function
 ```
 	.SYNOPSIS 
    A Powershell function used to send request to ripe whois rest-like interface this function is used by other functions included in this module like : Get-Whois, Get-WhoisSources, Get-WhoisObjectTemplateInfo
    HTTP Status Codes :
    code	description
    200		Search successful
    400		Illegal input - incorrect value in one or more of the parameters
    404		No object(s) found

    .DESCRIPTION
    Please consult Wiki available on Ripe-NCC github :
    https://github.com/RIPE-NCC/whois/wiki/WHOIS-REST-API-search
    Please consult WhoIs query reference maunal :
	https://www.ripe.net/manage-ips-and-asns/db/support/documentation/ripe-database-query-reference-manual
	
    .PARAMETER APIType
    -APIType string from the follozing list : 'search','metadata-source','metadata-object','abuse'
    Mandatory
    'search' : use the search API
    'metadata-source' : use the metadata API and request the source object
    'metadata-object' : use the metadata API and request a template object
    'abuse' : use the abuse API

    .PARAMETER Value
	-Value string
    Mandadatory
    part of the URL with value and parameters required to call the APIType set

    .PARAMETER NoTLS
    -NoTLS {Switch}
    Optional
    Don't use TLS to communicate with RIPE server (HTTP only)
    
	.OUTPUTS
	   TypeName : System.Management.Automation.PSCustomObject

        Name                 MemberType   Definition
        ----                 ----------   ----------
        Equals               Method       bool Equals(System.Object obj)
        GetHashCode          Method       int GetHashCode()
        GetType              Method       type GetType()
        ToString             Method       string ToString()
        objects              NoteProperty System.Management.Automation.PSCustomObject objects=@{object=System.Object[]}
        parameters           NoteProperty System.Management.Automation.PSCustomObject parameters=@{inverse-lookup=; type-fil...
        service              NoteProperty System.Management.Automation.PSCustomObject service=@{name=search}
        terms-and-conditions NoteProperty System.Management.Automation.PSCustomObject terms-and-conditions=@{type=locator; h...
        
        Name        MemberType   Definition
        ----        ----------   ----------
        Equals      Method       bool Equals(System.Object obj)
        GetHashCode Method       int GetHashCode()
        GetType     Method       type GetType()
        ToString    Method       string ToString()
        attributes  NoteProperty System.Management.Automation.PSCustomObject attributes=@{attribute=System.Object[]}
        link        NoteProperty System.Management.Automation.PSCustomObject link=@{type=locator; href=http://rest.db.ripe.n...
        primary-key NoteProperty System.Management.Automation.PSCustomObject primary-key=@{attribute=System.Object[]}
        source      NoteProperty System.Management.Automation.PSCustomObject source=@{id=apnic-grs}
 ```
 ###  Get-WhoisObjectFilterFromString function
 ```
 	.SYNOPSIS 
    internal function used by Get-Whois to retrieve the appropriate filter for a search value.

    .DESCRIPTION 
    internal function used by Get-Whois to retrieve the appropriate filter for a search value.
	
	.PARAMETER InputString
	-InputString string
    Mandadatory
    Search term, could be : ip, as number, org...
    
	.OUTPUTS
       TypeName : System.String

            Name             MemberType            Definition
            ----             ----------            ----------
            Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
            CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), int IComparable.CompareTo(System.Object obj), int IComparable[string].CompareTo(string other)
            Contains         Method                bool Contains(string value)
            CopyTo           Method                void CopyTo(int sourceIndex, char[] destination, int destinationIndex, int count)
            EndsWith         Method                bool EndsWith(string value), bool EndsWith(string value, System.StringComparison comparisonType), bool EndsWith(string value, bool ignoreCase, cultureinfo culture)
            Equals           Method                bool Equals(System.Object obj), bool Equals(string value), bool Equals(string value, System.StringComparison comparisonType), bool IEquatable[string].Equals(string other)
            GetEnumerator    Method                System.CharEnumerator GetEnumerator(), System.Collections.IEnumerator IEnumerable.GetEnumerator(), System.Collections.Generic.IEnumerator[char] IEnumerable[char].GetEnumerator()
            GetHashCode      Method                int GetHashCode()
            GetType          Method                type GetType()
 ```
###  Get-WhoisObjectInverseKeyFromObject function
 ```
 	.SYNOPSIS 
    use Get-WhoisObjectTemplateInfo to retrieve reverse lookup attribute for a whois object
    
    .DESCRIPTION
    use Get-WhoisObjectTemplateInfo to retrieve reverse lookup attribute for a whois object
	
	.PARAMETER WhoisObject
	-WhoisObject string from the following list : 'as-block','as-set','aut-num','domain','filter-set','inet6num','inetnum','inet-rtr','irt','key-cert','mntner','organisation','peering-set','person','poem','poetic-form','role','route','route6','route-set','rtr-set'
    Mandadatory

    .PARAMETER SimpleOutput 
    -SimpleOutput {Switch}
    Optional
    filter the powershell object sent back from cdmlet to show attributes property

	.OUTPUTS
        TypeName : System.Management.Automation.PSCustomObject

            Name        MemberType   Definition
            ----        ----------   ----------
            Equals      Method       bool Equals(System.Object obj)
            GetHashCode Method       int GetHashCode()
            GetType     Method       type GetType()
            ToString    Method       string ToString()
            cardinality NoteProperty string cardinality=MULTIPLE
            keys        NoteProperty Object[] keys=System.Object[]
            name        NoteProperty string name=mbrs-by-ref
            requirement NoteProperty string requirement=OPTIONAL
        
        TypeName : System.String

            Name             MemberType            Definition
            ----             ----------            ----------
            Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
            CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), int IComparable.CompareTo(System.Object obj), int IComparable[string].CompareTo(string other)
            Contains         Method                bool Contains(string value)
            CopyTo           Method                void CopyTo(int sourceIndex, char[] destination, int destinationIndex, int count)
            EndsWith         Method                bool EndsWith(string value), bool EndsWith(string value, System.StringComparison comparisonType), bool EndsWith(string value, bool ignoreCase, cultureinfo culture)
            Equals           Method                bool Equals(System.Object obj), bool Equals(string value), bool Equals(string value, System.StringComparison comparisonType), bool IEquatable[string].Equals(string other)
            GetEnumerator    Method                System.CharEnumerator GetEnumerator(), System.Collections.IEnumerator IEnumerable.GetEnumerator(), System.Collections.Generic.IEnumerator[char] IEnumerable[char].GetEnumerator()
            GetHashCode      Method                int GetHashCode()
            GetType          Method                type GetType()
            ...
   
    .EXAMPLE
    get INVERSE_KEY attribute foras-set whois template object
    C:\PS> Get-WhoisObjectInverseKeyFromObject -WhoisObject as-set

    .EXAMPLE
    get INVERSE_KEY attribute foras-set whois template object with a simplify output
    C:\PS> Get-WhoisObjectInverseKeyFromObject -WhoisObject as-set -SimpleOutput
 ```
 ###  Get-WhoisAllObjectsInverseKey function
 ```
 	.SYNOPSIS 
    use Get-WhoisObjectInverseKeyFromObject to retrieve all reverse lookup attribute for all whois template objects

    .DESCRIPTION
    use Get-WhoisObjectInverseKeyFromObject to retrieve all reverse lookup attribute for all whois template objects
    
    .PARAMETER SimpleOutput 
    -SimpleOutput {Switch}
    Optional
    filter the powershell object sent back from cdmlet to show attributes property

	.OUTPUTS
        TypeName : System.Management.Automation.PSCustomObject

            Name        MemberType   Definition
            ----        ----------   ----------
            Equals      Method       bool Equals(System.Object obj)
            GetHashCode Method       int GetHashCode()
            GetType     Method       type GetType()
            ToString    Method       string ToString()
            cardinality NoteProperty string cardinality=MULTIPLE
            keys        NoteProperty Object[] keys=System.Object[]
            name        NoteProperty string name=org
            requirement NoteProperty string requirement=OPTIONAL        

        TypeName : System.String

            Name             MemberType            Definition
            ----             ----------            ----------
            Clone            Method                System.Object Clone(), System.Object ICloneable.Clone()
            CompareTo        Method                int CompareTo(System.Object value), int CompareTo(string strB), int IComparable.CompareTo(System.Object obj), int IComparable[string].CompareTo(string other)
            Contains         Method                bool Contains(string value)
            CopyTo           Method                void CopyTo(int sourceIndex, char[] destination, int destinationIndex, int count)
            EndsWith         Method                bool EndsWith(string value), bool EndsWith(string value, System.StringComparison comparisonType), bool EndsWith(string value, bool ignoreCase, cultureinfo culture)
            Equals           Method                bool Equals(System.Object obj), bool Equals(string value), bool Equals(string value, System.StringComparison comparisonType), bool IEquatable[string].Equals(string other)
            GetEnumerator    Method                System.CharEnumerator GetEnumerator(), System.Collections.IEnumerator IEnumerable.GetEnumerator(), System.Collections.Generic.IEnumerator[char] IEnumerable[char].GetEnumerator()
            GetHashCode      Method                int GetHashCode()
            GetType          Method                type GetType()
            ...
 ```
 ###  Update-WhoisInfoAsVariable function
 ```
 	.SYNOPSIS 
    Set Whois sources and inverse_key object attribute as global variable or env variable (GLBWhoisSources and GLBWhoisObjetsInverseKey)
    Those variables are automatically checked by Get-Whois main function

    .DESCRIPTION
    Set Whois sources and inverse_key object attribute as global variable or env variable (GLBWhoisSources and GLBWhoisObjetsInverseKey)
    Those variables are automatically checked by Get-Whois main function

    .PARAMETER Clear 
    -Clear {Switch}
    Optional
    Clear all variables

    .PARAMETER Scope
    -Scope string from this list : 'GlobalVariable','EnvVariable'
    Optional
    'GlobalVariable' : set scope to global variable
    'EnvVariable' : set scope to env. variable
	    
	.OUTPUTS
    none
            
    .EXAMPLE
    clear all env and global variable
    C:\PS> Update-WhoisInfoAsVariable -clear

    .EXAMPLE
    set content as global variable
    C:\PS> Update-WhoisInfoAsVariable -scope GlobalVariable

    .EXAMPLE
    set content as env variable
    C:\PS> Update-WhoisInfoAsVariable -scope EnvVariable
 ```
