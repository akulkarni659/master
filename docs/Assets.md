[Back](Procedures.md?id=assets)

# Assets 
We have different types of assets available, 

> - Subscription VOD(SVOD)
> - Linear
> - StartOver(SO)
> - Catch-Up(CU)
> - Rented VOD(TVOD) 

To get information of all available assets in the environment requires scanning through all the channels(collections) available in GetMenu. 
Also, its important that the asset you selected for playback should be part of the entitlements available in domain. 
To make it easy and smooth for the end user, this `service` is implemented. 
Using this service you can get list of SVODs assets, Linear live assets, SO assets, CU 
assets based on subscription/entitlements the domain have and TVODs (_Need to be rented_).

- Below `services` have been implemented in `Assets`.

	- _GET_SVOD_ASSETS_
	- This `service` can be used to get all entitled SVOD assets available to your domain.

<!-- no toc --> 



[Back](Procedures.md?id=assets)
	