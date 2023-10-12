[Back](Procedures.md?id=subscriptions)

# Subscriptions

Subscriptions purchase flow requires a lot of authentications at different 3rd parties like Nagra, VRM. 
Missing validation at any of the vendors can lead issue skipping which is not at all acceptable.
To reduce the chance of issues skip, these procedures has been implemented that will ease the purchase 
internal complexity and at the same time will perform different required validations.
<br> The services under `Subscriptions` can be used for purchasing different types of subscriptions to domain. 
Like- nPVR, Linear, Mixed, SVOD, Collections, Folders. We can use these `services` in `beforeSuite` , `beforeTest` and also in `test`.<br>
<br><u>**Note- At all places variable '`subscriptionIds`' should be an ArrayList. When you transpose make sure it's a List, if not the tests will start failing.
In `declare` we can not pass as 'subscriptionIds[?]', so try to return using JAVA methods.**</u>
<br><br>Below services are implemented in `Subscriptions`

> `IDENTIFY_ALL_AVAILABLE_PACKAGED_SUBSCRIPTIONS_AVAILABLE_AT_ENVIRONMENT_LEVEL` <p> This `service` 
> can be used to get all available packaged based subscription avaialble at environment level. 
> For Example - In ATP it will return all Linear, Mixed, SVOD, nPVR susbcriptions available in ATP environment.
> <br> This service returns a variable `subscriptionIds` to be consumed in purchase service.

> `IDENTIFY_BLOCKABLE_AND_NON_BLOCKABLE_PVL_PACKAGED_SUBSCRIPTIONS_AT_ENVIRONMENT_LEVEL_VIA_PH2_URL` <p>
> This `service` can be used to get Blockable and Non-Blockable subscriptions list of type - Linear, Mixed, SVOD 
> for Provisioning Layer based test scenarios.
> <br> This service return below variables, which can be consumed by transposing to `subscriptionIds` variable in purchase flow -
>  <br>
>> 1. `skyBlockablePackages[?]`
>> 2. `skyNonBlockablePackages[?]`
>> 3. `vfkdBlockablePackages[?]`
>> 4. `vfkdNonBlockablePackages[?]`
>> 5. `linearBlockableSubs[?]`
>> 6. `mixedBlockableSubs[?]`
>> 7. `svodBlockableSubs[?]`
>> 8. `linearNonBlockableSubs[?]`
>> 9. `mixedNonBlockableSubs[?]`
>> 10. `svodNonBlockableSubs[?]`

> `GET_ALL_MIXED_SUBSCRIPTIONS_AT_ENVIRONMENT_LEVEL` <p>
> This service can be used to purchase Mixed type of subscriptions(_Subscriptions haveing both Lnear and SVOD contents_).
> <br> This service returns variable - `mixedSubs`(VFKD VSP only), `mixedSubsAll[?]`(All VSPs) and transpose of `mixedSubs` to `subscriptionIds`
> to be consumed in purchase flow. Here, we are giving you already transposed `subscriptionIds` value if you want to directly purchase all mixedSubs.

> `GET_ALL_LINEAR_ONLY_SUBSCRIPTIONS_AT_ENVIRONMENT_LEVEL` <p>
> This service can be used to purchase Linear type of subscriptions.
> <br> This service returns variable - `linearSubs`(VFKD VSP only), `linearSubsAll[?]`(All VSPs) and transpose of `linearSubs` to `subscriptionIds`
> to be consumed in purchase flow. Here, we are giving you already transposed `subscriptionIds` value if you want to directly purchase all linearSubs.

> `GET_ALL_SVOD_ONLY_SUBSCRIPTIONS_AT_ENVIRONMENT_LEVEL` <p>
> This service can be used to purchase SVOD type of subscriptions.
> <br> This service returns variable - `svod`(VFKD VSP only), `svodAll[?]`(All VSPs) and transpose of `svod` to `subscriptionIds`
> to be consumed in purchase flow. Here, we are giving you already transposed `subscriptionIds` value if you want to directly purchase all svod subscriptions.

> `GET_ALL_VRM_nPVR_PREMIUM_SUBSCRIPTIONS_AT_ENVIRONMENT_LEVEL` <p>
> This service can be used to purchase nPVR type of subscriptions.
> <br> This service returns variable - `npvrPremiumSubs`. While purchasing, you can get a random subscription from the `npvrPremiumSubs` list and transpose it to 
> `subscriptionIds` variable to be consumed in purchase flow. _Refer Note at top for transposing to `subscriptionIds`._

> `VERIFY_NPVR_SUBSCRIPTION_QUOTA` <p>
> This service can be used to verify the nPVR(_VRM_)`Quota` allocated to domain after purchasing a premium nPVR subscriptions from above service `GET_ALL_VRM_nPVR_PREMIUM_SUBSCRIPTIONS_AT_ENVIRONMENT_LEVEL`.
> This service requires `subscriptionIds[?]`(List containing single purchased nPVR subscriptions) variable for its processing.<br>
> You just have to call this service after purchasing the nPVR subscription.
> <br> You can use like below-
> <br>
>> - `- !procedure root:\Procedures\Subscriptions.yaml:GET_ALL_VRM_nPVR_PREMIUM_SUBSCRIPTIONS_AT_ENVIRONMENT_LEVEL`
>> - Transpose in declare or in any other step- `subscriptionIds: method:com.vodafone.tv.extractMethods.ExtractionHelpers.getSubArrayList({npvrPremiumSubs},0,1)`
>> - `- !procedure root:\Procedures\Subscriptions.yaml:ADD_SUBSCRIPTIONS_TO_DOMAIN`
>> - `- !procedure root:\Procedures\Subscriptions.yaml:VERIFY_NPVR_SUBSCRIPTION_QUOTA`

> `ADD_SUBSCRIPTIONS_TO_DOMAIN` <p>
> This service can be used for packaged subscription purchase flow. It requires variable `subscriptionIds` for its processing.
> <br> Under this service all subscriptions available in variable `subscriptionIds` is getting purchased at Kaltura. The purchased subscriptions are then verified at Kaltura and 
> Nagra(Entitlement and domain check). At Nagra by subscription are verified for which `IsNagraCAPackage:false`.
> <br> The below variables are returned in this service-
> <br>
>> 1. `subs, price, currency`- List of all purchased subscriptions. This gets updated with new set of susbcriptions if this service is called again for different set of susbcriptions.
>> 2. `subsVerifyNagra`- List of subscriptions available to be verified at Nagra only.
>> 3. `purchasedSubs`- List of all purchased subscriptions. All new subscriptions purchased gets added to this variable.
>> 4. `allPackgedSubscription`
>> 5. `IsNagraCAPackageTrueSub`
>> 6. `IsNagraCAPackageFalseSub`

> `ADD_SUBSCRIPTIONS_TO_DOMAIN_VIA_PH2_URL` <p>
> This service is exactly same as `ADD_SUBSCRIPTIONS_TO_DOMAIN`. This service is using APIGW-BE Phase2 URLs for VFDE specific. 



[Back](Procedures.md?id=subscriptions)