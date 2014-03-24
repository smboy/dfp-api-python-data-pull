dfp-api-python-data-pull
========================

Google data extract using Dfp Api python using a generic framework

Prelim setup:
--------------
Following the instructions in the below page and download the required library from google.
https://github.com/googleads/googleads-python-lib


(work in progress...but as such the program should work without any issues)

dfp_config.py
-------------
Insert the client_id, client_secret, refresh_token, networkCode

from googleads import dfp, oauth2
oauth2_client = oauth2.GoogleRefreshTokenClient(
        client_id='THIS_IS_THE_CLIENT_ID_GENERATED_DURING_THE_OAUTH_PROCESS',
        client_secret='THIS_IS_CLIENT_SECRET',
        refresh_token='this_is_refresh_token_required_for_refreshing_access_token'
       )
client = dfp.DfpClient(oauth2_client, 'google_dfp_prod')
networks = client.GetService('NetworkService').getAllNetworks()
network = networks[0]['networkCode']
print "Network code : %s" % network
client = dfp.DfpClient(oauth2_client,'google_dfp_prod', network)


dfp_param.py
-------------
[Order]
dfpservice = OrderService
dfpstmt = getOrdersByStatement
#columns = 'advertiserId','id','name','traffickerId','secondaryTraffickerIds','salespersonId','totalClicksDelivered','totalImpressionsDelivered','startDateTime','endDateTime','lastModifiedDateTime','currencyCode','status'
#columns = data['advertiserId']|data['id']|data['name'].encode('utf8')|data['traffickerId']|data.get('secondaryTraffickerIds','')|data.get('salespersonId','')|data['totalClicksDelivered']|data['totalImpressionsDelivered']|_ConvertDateFormat(data['startDateTime'])|_ConvertDateFormat(data.get('endDateTime',None))|_ConvertDateFormat(data['lastModifiedDateTime'])|data['currencyCode']|data['status']
filename = order.output

[Advertiser]
dfpservice = CompanyService
dfpstmt = getCompaniesByStatement
columns = [a['id']]
filename = advertiser.out

[DEFAULT]
API_VERSION = v201403
page_size = 500
offset = 0


dfp_api_generic.py
------------------

1. This is the main program that executes. Currently the list of columns etc are all part of this. Once the program is completely built, the list of columns etc will be part of the dfp_param.py
2. There is an additional step in this program that does list iteration to rows. For example: secondarytrafficker_ids can be multiple. or salesperson_ids can be multiple. in that case, it will be a nested List within the dict object when we get the data from api. The program automatically converts that to multiple rows (normalize) repeating other columns.
