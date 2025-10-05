## **1. RLS Implementation**  

**Static vs Dynamic RLS:**  

```dax  
// Static RLS (hard-coded values)  
[Region] = "North"  

// Dynamic RLS (user-based)  
[Region] = LOOKUPVALUE(  
    UserSecurity[Region],  
    UserSecurity[Email],  
    USERPRINCIPALNAME()  
)  
```  

> [!IMPORTANT]  
> **Key Difference**: Static RLS uses fixed values, while dynamic RLS references user attributes from a security table.  
