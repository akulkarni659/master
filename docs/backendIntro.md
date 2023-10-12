[Back](/README.md)

# vtv-backend-tests

This repository stores all the test cases. all the test cases are stored under a specific test suites

## swagger definitions

We get the swagger definitions from the component team and they need to uploaded on the platform where the test framework is executed from. The process below explains how to do that -
1. Swagger file may be provided by components in **yaml** or in **json** format. our platform expect the swagger file in json format and to the Open specification definition to version 3.0.1.
2. There are instances when the swagger files acreated per service within the component e.g. Cellfocus component. In such a case, we need to combine the definitions in one file.
3. The above activties should be done using the swagger editor. [link](https://editor.swagger.io/). Copy the definitions in the editor and update what is required e.g. combining the multiple API definitions in one or if the specifications are available in lower version, we should convert it to the required version using the swagger editor
4. replace all the references to **"application/json"** under http code e.g. 200 and so on with */* (* followed by backslash followed by * in case its not visible correctly)
5. Once all the edits are done, there should be no errors reported in the window on the right in swagger editor
6. At this stage, the yaml file should be converted to json. This can be done by selecting the File - > convert and save as json option
7. rename the file to the name of the specification. FTP it on the folder where the open api specification have been stored. This is explained in details on osh configuration pages refer to swagger component section.
8. update the **application.yaml** on swagger repository and provide the urls and name to specification.
9. Update the config map of the test pod to strt using the new api specifications
10. Restart the swagger pod, the new changes will take effect
11. Test the requests/responses with -
    Swagger-Component exposes below REST APIs

* to get a request for a operation id of a spec. Note that REST/GET does not have request body /get/request/{specName}/{operationId}
* Generates sample request payload where anyOfObject is present and you want a specific object to be included in the request /get/requestAnyOf/{specName}/{operationId}?section={requestSection}&anyOfObject={anyOfObject}
* Generates list of all the spec currently configured /get/specList
* Generates response jsonSchema - /get/jsonschema/{specName}/{operationId}/{responseCode}

Swagger UI can be accessed via http://{host}:8080/swagger-ui.html

# Overhead handling

It is understood that it's not always possible to have the swagger specification up to date or in some cases due to poor design, specification does not reflect the mandatory fields in the specification. VTV invent framework allows the users to update the requrests and responses for specific APIs when the sample requests and responses are generated.

It is possible to manually edit the swagger specification to meet the QA needs but since the API specifications are owned by the components and are subject to change, making the changes in spec by QA resources is discouraged and it should instead be handled via overhead handling.

Currently, the overheads are supported at 2 levels.

-   Swagger Specification level - When defined at this level, it is applied to every API **response** schema generates for that specification and hence this overhead should be defined carefully. All the validations will be performed against the schema generated after the specification level overrides are applied. If the spec level overheads does not meed the requirements, alternatively option is to use operation level overheads
-   Operation level - This is applied to a specific API within the spec that is identified using the operationId. Changes likse data types, addition of new fields that are not available in the specification, make certain fields mandatory etc. are the candidates for the operation level overheads. While Specification level overriding is allowed for responses, operation level supports both requests and responses.

If the spec level and operation level overheads are defined, both will be applied when the **response schema** is generated

The section below provides an example of the override. The template of the supplementary.yaml file is as below

```
operations:
  - operationId: "entitlement.list"
    request:
      $.userId: null
      $.filter.objectType: "KalturaEntitlementFilter"
  - operationId: "ottUser.login"
    request:
      $.extraParams: null
    response:
      $.properties.result.properties.user.properties.dynamicData.type: "object"
      required:
        - $.result.loginSession
        - $.result.loginSession.ks
specLevelOverheads:
  - specName: Phoenix
    schemaOverhead:
      $.properties.result.properties: $.properties
      $.properties.result.type: $.type
      $.type: "object"
      $.properties.executionTime.type: "number"      
```

- **operations** - provide a list of all the operations to be modified across all the swagger files that are being supported by the swagger service. This is a common file for the entire service

    - **operationId** - As it is defined in the swagger specification. Operation id should be unique across all the specifications if they are likely to be modified via overheads
    - **request** - to contain all the attributes that need to be modified and should be provided as per flattened json e.g. $.result.objects etc. It should contain all the properties and the changes that is to be applied on top of the swagger file. All the fields mentioned here, will be added on top of the request if they do not exist  or will be modified if they do exist **(Parag to check)**. Value null means the field will be available in the sample request with null value which the tester need to update in the request via test case if required
    - **response** - can also add few fields but when added, it has to be provided in flattened structure of the object as it would be defined in swagger file. This is because as against a request where the supplementary information is added to the sample json request that is created after parsing the swagger schema, response is always generated as schema to validate the API response. Before adding the attributes here, make sure via swagger editor that the structure provided does not invalidate the schema

        - **required** - You can specify all the fields/objects that are mandatory in response if they are not specified as mandatory in the specification. Note that the fields to be specified as per the flattened API json response and not the swagger schema. This is because the required overheads are applied after the json schema has been created by parsing the definition from swagger specifcaion and by applying the spec/operation level overheads. The swagger component has a reference to the possible json respons and can deduce the path within schema where required definition to be applied. If the new field is added via spec/operation level overheads, they can be defined as manadatory/required via this mechanism also. All the fields to be made mandatory should be provided as array list of fields with full qualification.

- **specLevelOverheads** - includes all the specification as list object where the spec level overrides to be provided. Since we needed it to be used only for the Kaltura, the configuration and how it works is explained below -

As per Kaltura swagger specification the response for **/service/entitlement/action/list** API is as below. But in reality, response is wrapped inside **result** object and has extra parameter **executionTime**. This is not just one API but this is being applied to all the APIs. There are 400+ APIs and though not all of them being used by VTV solution, however modifying them in specification is quite challenging also changing them at the operation level is not feasible either. So we needed a better mechanism to address this simplistically.
```
 {
  "objectType": "KalturaListResponse",
  "totalCount": 0,
  "objects": [
    {
      "currentDate": 0,
      "currentUses": 0,
      "deviceName": "string",
      "deviceUdid": "string",
      "endDate": 0,
      "householdId": 0,
      "id": 0,
      "isCancelationWindowEnabled": true,
      "lastViewDate": 0,
      "maxUses": 0,
      "objectType": "KalturaEntitlement",
      "paymentMethod": "unknown",
      "productId": "string",
      "purchaseDate": 0,
      "userId": "string"
    }
  ]
  }
```

The spec level override definition as below

```
 specLevelOverheads:
  - specName: Phoenix
    schemaOverhead:
      $.properties.result.properties: $.properties
      $.properties.result.type: $.type
      $.type: "object"
      $.properties.executionTime.type: "number"
```

1. **$.properties.result.properties** - A new property is added at root level because the response starts with the **result** object and this need to be applied to every response schema for the spec. The value of this is to be set to what is currently defined at root level in the swagger specification i.e. $.properties. So the new definition take the new object **result** that has everything that is currently defined for that API response in the swagger definition file is assigned to the **result** object.
2. **$.properties.result.type** - As per the open API specification, every API attribute that is created/defined should have the object type associated with it e.g. string, number, object (json), array etc. Since the new object that was added and assigned the value, has not yet been defined as what type of object it is, So we just apply the $.type which is the current value of the API response schema as per swagger file and whatever it is for a specific API is then passed to the **result** object
3. **$.type** - is associated to $ object which means after applying the result object definitions, what kind of object the whole response is now. Majority of the cases it is json object. So we define it as of type object. There are few cases where response returned is **true** or **None**. These are identified as non json object and in that case, framework does not need to compare against the response schema generated for the validation.
4. **$.properties.executionTime.type**: "number" is added at root level (same level as result) and its type is number. 

