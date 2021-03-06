* this.BilltoParty.IsSet()

this.BilltoParty.IsSet():References to business object nodes (associations) have to be checked using the IsSet() method before accessing them. If a reference that is not set is accessed, the system will terminate the program.

# Query Related

```abap
import AP.Common.GDT;
import AP.CRM.Global;
var SalesOrderquery = SalesOrder.QueryByElements;
var queryParameter = SalesOrderquery.CreateSelectionParams();
var queryResult;
queryParameter.Add(SalesOrderquery.ID.Content,"I", "EQ","1686");
queryResult = SalesOrderquery.Execute(queryParameter);
foreach( item in queryResult )
     item.ext_test_for_lock = "change by Service Order";
```

# collection

import ABSL;
import AP.Common.GDT

```abap
var mycollection : collectionof SalesOrder.BuyerID; // internal table
```

# element

```abap
var mycollection: collectionof SalesOrder.BuyerID;
var result: collectionof SalesOrder.BuyerID;

var item_one: elementof SalresOrder.BuyerID;
var item_two: elementof SalresOrder.BuyerID;
var collection: collectionof SalesOrder.SystemAdministrativeData;

item_one.content = "A";
mycollection.Add( item_one);

item_one.content = "B";
mycollection.Add( item_one);
mycollection.Count();
item_two = mycollection.getFirst();
item_two.Clear();
item_two = mycollection.getLast();
item_two.Clear();
collection.OrderBy( time=>time.LastChangeDateTime);
result = mycollection.Where( test=>test.content == "A" );
```

# Retrieve

```abap
var SalesOrderID : elementsof SalesOrder.ID; 
SalesOrderID.content = "12";
SalesOrder.Retrieve( SalesOrderID );

<node Instance> = <BusinessObject>.Retrieve(<alternativeKey>);
<Collection of node instances> = <BusinessObject>.Retrieve(<collection of alternativeKeys>);

```

# String operation

```abap
import ABSL;
var test;
test = String.Substring("Test",2,2);
```

# Reuse Service - Get current date time

```abap
import ABSL;
var time;
time = Context.GetCurrentGlobalDateTime( );
``` 

# Where

```abap
var mySearchValues : elementsof myBO.Item;
var collectionA;  
mySearchValues.ItemID.content = "Bill";
collectionA = this.Where(n => n.ItemID == mySearchValues.ItemID || !(n.ItemID.content < 1000 && n.ItemID.content == "Fred")); 
```

### Limitations

* Collections with an unstructured table line are not supported.
* It is not possible to delete instances from a BO node with the where-statement. The delete method has to be used for this purpose. 

# DistinctBy

<Collection>.DistinctBy(<anonymous function>).ThenBy(<anonymous function>);


The DistinctBy statement deletes duplicate lines in collections by comparing content of defined components.
An anonymous function (lambda expression) is used to refer to the components of the collection line type.

collection.DistinctBy(n => n.BUYER_NAME.CONTENT).ThenBy(n => n.STATUS).ThenBy(n => n.TYPE_CODE); 

# Count

```abap
<intVar> = <Collection>.Count();
```

# BODL

* Assignment of Deviating Deployment Unit

[DeploymentUnit(CustomerRelationshipManagement)] businessobject BonusPlan {
    element ID : Identifier;
}

[DeploymentUnit(CustomerRelationshipManagement)]
[Extension]
businessobject BonusPlan {
                     element ID : Identifier;
}

* DO - dependent object

[DependentObject(TextCollection)] node ItemTextCollection;

* annotation combination

1. [AlternativeKey] element AssigmentDate : Date;
2. [CrossDeploymentUnit] association toBusinessPartner to BusinessPartner;
3. [RelevantForAccessControl] association toBusinessPartner to BusinessPartner;
4. [DependentObject(TextCollection)] node TextCollection; 
5. [Extension] businessobject SalesOrder { ... }
6. [DeploymentUnit(CRM)] businessobject SunnyDay { ... }
7. [DependentObject(TextCollection)] [MultipleTexts] node TextCollection; //second annotation

* BOGenerationHandler.cs

```c#
using System;
using System.Collections.Generic;
using System.Text;
using com.sap.JSONConnector;
using SAP.Copernicus.Core.Protocol.JSON.ProxyClasses;
using SAP.Copernicus.Core.Repository;
namespace SAP.Copernicus.Core.Protocol.JSON.Handlers
/// Generates an BO that must already exist in the MDRS. Generation consists of the following three steps:
        /// Proxy Generation, automated BOPF implementation, Script Code Snippet generation and registration.
        /// Script code context parameters must not be provided anymore, as the RFC analyses the existence of script
        /// code in the backend (XRep) and automatically registers the BOPF determination impl classes accordingly.
// Get cached JSON Client for currently connected Respository system
                JSONClient jsonClient = Client.getInstance().getJSONClient();
                // Create Proxy
                PDI_RI_BOG_GENERATE proxy = new PDI_RI_BOG_GENERATE();
                // Handle params
                proxy.Importing = new PDI_RI_BOG_GENERATE.ImportingType();
                proxy.Importing.IV_ESR_NAMESPACE = nsName;
                proxy.Importing.IV_ESR_BO_NAME = boName;
                proxy.Importing.IV_PROXY = "X";
                proxy.Importing.IV_BOPF = "X";
                // Execute the RFC
                jsonClient.callFunctionModule(proxy);
```