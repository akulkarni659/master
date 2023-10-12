[Back](/README.md)

# vtv-core
Core Automation Framework 2.2 is rewrite from the earlier version with many new changes have been introduced. Besides hook, following high level changes without which the framework will not work are as below -
1. It will need new http plugin version 2.2 to be available. A plugin base has changes added to it to support mediaType and an additional parameter added so older http plugin version is not compatible with the framework core 2.2.
2. A new hook version is needed. Besides mediaType change implicitly impacting hook to call the Kaltura API for signature generation need, the dependency on the context variable is removed significantly. Currently only partnerId is required to be provided. Previously KS was mandatorily required to be set as contest variable because hook was using this information from the context variable and test steps/cases whether they are using the ks or not, needed to set the ks variable somewhere. There is no need. See further documentation of the hook.
3. Current version does not support MQTT plugin as there is no binding. This support will be added soon