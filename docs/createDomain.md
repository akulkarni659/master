[Back](Procedures.md?id=createdomain)

# Create Domain
The services under `createDomain` are the base service and all other services depends on this. These services are created to reduce the 
effort of creating Domains/Devices/Profiles by each individual while creating a new test pack or a single test.
Creating domain, adding devices and adding profiles are not a very straight forward things and requires deep understanding of VTV Flows. 
This was causing lot of discrepancies and differences across test suites and also validation checks were not thoroughly checked at 3rd parties like NAGRA. 
We can call these `Services` in `beforeSuite` , `beforeTest` and also in `test`.

## Services in `createDomain`

> `GET_ADMINUSER_DMS_DEVICEBRANDS_DETAILS`

	This service can be used to get details of `DMSConfig`, `OperatorKS` and `deviceBrands`.
	This service requires given variables for its processing from global config- 
	`appname`, `cver`, `platform`, `udid`, `userOperator`, `passOperator`,`partnerid`,`apiVersion`

	Below variables are returned in this service-

		- `apiVersion`
		- `partnerid`
		- `udid` (udid of global user)
		- `apiUser`
		- `apiPass`
		- `platform`
		- `mainMenuId`
		- `movieType`
		- `linearType`
		- `episodeType`
		- `seriesType`
		- `folderType`
		- `packageType`
		- `ottFileTypes`
		- `dvbcFileTypes`
		- `OTTFileToDeviceProfiles`
		- `adminks`
		- `stbFamilyId`
		- `stbBrandId`


> `CREATE_DOMAIN_ONLY_IN_KALTURA` 

	This `service` can be used to create domain at **Kaltura** only and set paymentGateways at Kaltura 
	alongside it does all the required verifications. This will not provision the domain on **Nagra**(Device and Entitlement management vendor) 
	or **VRM**(nPVR/cDVR vendor). The service requires few information to process, 
	Like-  _getConfig(DMS details), OperatorKS, PaymentGateways details_(_We get this information from a fully provisioned domain-Global User_).
	To ease this process we have also created two basic services 
	`GET_ADMINUSER_DMS_DEVICEBRANDS_DETAILS` and `GET_GLOBAL_USER_DETAILS`(_Details of these services are defined below_).
	These above mentioned two services you need to call before calling the service, as depicted below
	`- !procedure root:\Procedures\createDomain.yaml:GET_ADMINUSER_DMS_DEVICEBRANDS_DETAILS`
	`- !procedure root:\Procedures\createDomain.yaml:GET_GLOBAL_USER_DETAILS`
	`- !procedure root:\Procedures\createDomain.yaml:CREATE_DOMAIN_ONLY_AT_KALTURA`
	This service requires variables- `adminks`,`partnerid`,`Password`, `paymentGatewayName` for its processing.
	
	Below variables are returned in this service-
		1. `masterUid`
		2. `masterUsername`
		3. `testDomainId`
		4. `defaultUser`
		5. `defUser`(Its same as `defaultUser`. Will remove later and use only `defaultUser` variable)
   
    	NOTES 
    	1. Please select getConfig according to device.
		2. Please fetch the admin username and password in beforeSuite from Global Configuration.

> `CREATE_DOMAIN_ONLY_AT_KALTURA_VIA_PH2_URL` 

	This service is exactly same as `CREATE_DOMAIN_ONLY_IN_KALTURA` except that, all the expected/possible 
	flows are via APIGW-BE Phase2 URL(_Specific to VF DE_).

> `ADD_SINGLE_NEW_DEVICE_TO_DOMAIN` 

	This `service` can be used to add a single device to domain. By default, this service can add a single 
	STB device in the newly created domain. If you want to create a different devicetype, you need to add the below device details variables 
	before calling this service(_You can add the device details in `test declare:` as well._)</p>
		1. `dBrandId`
		2. `dUdid`
		3. `deviceType`
		4. `caSN`(_if STB_)

	Below variables are returned in this service- <br>
		1. `deviceUdid`
		2. `udid`
		3. `extraParams`
		4. `allAddedDevices`
		5. `defks`
		6. `ks` (Transform of`defks`)

> `ADD_SINGLE_NEW_DEVICE_TO_DOMAIN_VIA_PH2_URL` 

	This service is exactly same as ADD_SINGLE_NEW_DEVICE_TO_DOMAIN except that, 
	all the expected/possible flows are via APIGW-BE Phase 2 URL(Specific to VF DE).

> `ADD_SINGLE_NEW_DEVICE_TO_DOMAIN_FOR_ChromeCast` 

	This service is exactly same as ADD_SINGLE_NEW_DEVICE_TO_DOMAIN except the device type is Chromecast.</p>

> `ADD_SINGLE_NEW_DEVICE_TO_DOMAIN_FOR_ChromeCast_VIA_PH2_URL` 

	This service is exactly same as ADD_SINGLE_NEW_DEVICE_TO_DOMAIN 
	except the device is Chromecast and flows are via APIGW-BE Phase 2 URL(Specific to VF DE).

> `ADD_SINGLE_NEW_PROFILE_TO_DOMAIN` 

	This service can be used to add a new profile in the existing/newly created domain.
    This service depends on `CREATE_DOMAIN_ONLY_IN_KALTURA`, as it needs below information to process-</p>
		1. `masterUid` (Should be via FE)
		2. `adminks`
		3. `Password`

    Below variables are returned in this service- 
		1. `userProfileId`
		2. `profileUsername`
		3. `allUserProfileIds`(all child profiles-Non master/Default)
		4. `nonMasterUsers`
		5. `defUser`
		6. `ChildUser`(Random child userID)
		7. `allUsersList`(All users including master and default)


> Here is an example how you should call all above `createDomain` services-

- `- !procedure root:\Procedures\createDomain.yaml:GET_ADMINUSER_DMS_DEVICEBRANDS_DETAILS`
- `- !procedure root:\Procedures\createDomain.yaml:GET_GLOBAL_USER_DETAILS`
- `- !procedure root:\Procedures\createDomain.yaml:CREATE_DOMAIN_ONLY_IN_KALTURA`
- `- !procedure root:\Procedures\createDomain.yaml:ADD_SINGLE_NEW_DEVICE_TO_DOMAIN`
- `- !procedure root:\Procedures\createDomain.yaml:ADD_SINGLE_NEW_PROFILE_TO_DOMAIN`



[Back](Procedures.md?id=createdomain)