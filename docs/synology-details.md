# Synology Configuration Details

 1. Create a Storage Pool
 2. Create a Volume
 3. Go to Control Panel > Security > General: Enable "Enhance browser compatibility by skipping IP checking"
 4. Go to Control Panel > Security > Account: Disable "Auto block"
 5. Create a Storage User service account and add it to the "administrators" group

## FAQ

As multiple logins are executed from this service at almost the same time, your Synology might block the
requests and you will see `407` errors (with version 6) or `400` errors in your log. It is advisable to
disable Auto block and IP checking if you want to get this working properly.

Make sure you do the following:
- go to Control Panel / Security / General: Enable "Enhance browser compatibility by skipping IP checking"
- go to Control Panel / Security / Account: Disable "Auto block"
