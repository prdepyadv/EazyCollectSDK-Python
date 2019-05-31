# EazySDK - Python client

Welcome to the **EazySDK** repository.  EazySDK is an integration of the [Eazy Collect API version 3](https://eazycollectservices.github.io/EazyCollectAPIv3/) built in Python. Its core purpose is to provide a framework for developers already working with Eazy Collect to integrate Eazy Customer Manager into their platform. The framework provides functions designed to speed up the integration process between a developers Customer Relationship Manager and Eazy Collect. Getting started is as simple as providing user specific settings, and making your first call to Eazy Customer Manager should take less than a minute.

## Requirements

 - requests (2.21.0 >=)
 - python (3.7+)

## Integrating EazySDK into your application

The integration process is simple, and involves importing EazySDK into your virtual environment and configuring some settings. The most basic configuration can be seen below.

    >> pip install eazysdk
     
    import eazysdk
     
    eazysdk.settings.current_environment['env'] = 'sandbox'  
    eazysdk.settings.sandbox_client_details['client_code'] = '{client_code}'  
    eazysdk.settings.sandbox_client_details['api_key'] = '{api_key}'
     
    client = eazysdk.EazySDK()
    response = client.get.customers()
    
    print(response)

## Configuration

Configuring EazySDK is simple, and can be done in two ways. The settings file for EazySDK is located at `.settings`, or to configure settings inline, it's as easy as `eazysdk.settings.{setting}['{argument}']`.

### Available settings

#### current_environment

Defines the current working environment of EazySDK. As many of our clients own a sandbox test account, the ability to switch between the live and sandbox environment at ease is crucial.

##### Acceptable arguments

*sandbox*

The sandbox test environment. All data submitted to this environment will not be processed by BACS, and no collections will be taken from customers.

*ecm3*

The live working environment. All data submitted to this environment will be processed by BACS, sending test information to the live environment may result in a client being charged.

#### sandbox_client_details

The API authorisation details for the sandbox test environment.

##### Acceptable arguments

*client_code*

The sandbox environment client code used when making API calls to Eazy Customer Manager.

*api_key*

The sandbox environment API key used when making API calls to Eazy Customer Manager.


#### ecm3_client_details

The API authorisation details for the live working environment.

##### Acceptable arguments

*client_code*

The live working environment client code used when making API calls to Eazy Customer Manager.

*api_key*

The live working environment API key used when making API calls to Eazy Customer Manager.


#### direct_debit_processing_days

The number of days the SDK expects a Direct Debit take in processing. **NOTE:** This will not reduce the number of days any Direct Debits take to process, it will only change the number of days the SDK *expects* a Direct Debit to take in processing.  Changing any sub settings within this setting  ***will*** cause collections to be missed without prior arrangements being made. We ***STRONGLY*** advise these settings are not changed without prior consultation with Eazy Collect.

##### Acceptable arguments

*initial*

The length of time in days a Direct Debit takes to process when it is initially created. By default, this is set to `10` working days.

*ongoing*

The length of time in days a Direct Debit takes to process on subsequent payments. By default, this is set to `5` working days.

#### contracts

A collection of settings to automatically fix common mistakes made when creating contracts in Eazy Customer Manager.

##### Acceptable arguments

*auto_fix_start_date*

If the `start_date` is too soon, and this is set to `True`, the SDK will automatically set the `start_date` to the next available. By default, this is set to `False`.

*auto_fix_ad_hoc_termination_type*

Ad-Hoc contracts mandate that the `termination_type` is set to `Until further notice`. If this is set to `True`, the SDK will automatically set the `termination_type` to `Until further notice`. By default, this is set to `False`.

*auto_fix_ad_hoc_at_the_end*

Ad-Hoc contracts mandate that `at_the_end` is set to `Switch to further notice`. If this is set to `True`, the SDK will automatically set `at_the_end` to `Switch to further notice`. By default, this is set to `False`.

*auto_fix_payment_day_in_month*

If the `payment_day_in_month` does not match the day in `start_date`, and this is set to`True`, the SDK will automatically set the `payment_day_in_month` to the day in `start_date`. By default, this is set to `False`.

*auto_fix_payment_month_in_year*

If the `payment_month_in_year` does not match the year in `start_date`, and this is set to`True`, the SDK will automatically set the `payment_month_in_year` to the year in `start_date`. By default, this is set to `False`.


#### payments

A collection of settings to automatically fix common mistakes made when creating payments in Eazy Customer Manager.

##### Acceptable arguments

*auto_fix_payment_date*

If the `payment_date` is too soon,  and this is set to`True`, the SDK will automatically set the `payment_date` to the next available. By default, this is set to `False`.

*is_credit_allowed*

Defines whether or not a client can credit a customer. **NOTE:** This does not change whether or not a client can credit a customer, but whether or not the SDK *expects* a client can credit a customer. By default, this is set to `False`.


#### warnings

A collection of warnings to alert the client of potential consequences of using EazySDK.

##### Acceptable arguments

*customer_search*

If set to `True`, this will alert the client when searching for customers without any parameters that EazySDK may take some time to retrieve all customers belonging to the client. By default, this is set to `True`.

*ecm3*

The live working environment. All data submitted to this environment will be processed by BACS, sending test information to the live environment may result in a client being charged.


#### other

A collection of utilities functions used to streamline the process of using EazySDK.

##### Acceptable arguments

*bank_holidays_update_days*

Defines the number of days EazySDK will wait before pinging the [bank holidays json file](https://www.gov.uk/bank-holidays.json). By default this is set to `30`. We recommend leaving this setting at `30`.

*force_schedule_updates*

If this is set to `True`, every time a call is made through EazySDK which interacts with schedules EazySDK will call `get.schedules()` in the background, and save the contents to the `.includes` folder. We recommend leaving this setting as is, though if you are experiencing issues with schedules, this is a useful diagnostic tool.



## Using EazySDK

EazySDK has been designed with simplicity in mind. All functions used in the base framework originate from four different HTTP methods, `GET`, `POST`, `PATCH` and `DELETE`. If you'd like to delete a payment, it's as simple as `delete.payment({params})`. A list of all functions can be found below, but all also have a useful docstring which can be accessed with `help({function})` from within Python.

### get

Sends a `GET` request to EazyCustomerManager. Optionally, parameters may be passed. Returns a JSON object or a string.

#### callback_url

Get the currently active callback URL for data returned from EazyCustomerManager.

*Example*

get.callback_url()

*Returns*

string - 'The callback URL is {url}'


#### customers

Search EazyCustomerManager for a single or a set of customers. **Note** if `settings.warnings['customer_search']` is set to true, you will be warned if attempting to search for customers without passing any parameters.

*Optional parameters*

 - *email* - The full email address of a single customer
 - *title* - The title of a customer or a set of customers
 - *search_from* - Search for customers created on or after this date
 - *search_to* - Search for customers created on or before this date
 - *date_of_birth* - The full date of birth of a customer or a set of customers
 - *customer_reference* - The full or partial customer reference of a customer or a set of customers
 - *first_name* - The full or partial first name of a customer or a set of customers
 - *surname* -  The full or partial surname of a customer or a set  of customers
 - *company_name* - The full or partial company name of a customer or a set of customers
 - *post code* - The full or partial post code of a customer or a set of customers
 - *account_number* - The full bank  account number of a customer  
- *sort_code* - The full sort code of a customer or a set of  customers
 - *account_holder_name* - The full or partial account holder name of a customer or a set of customers
 -  *home phone* - The full or partial home phone number of a customer or a set of customers
 - *work_phone* - The full or partial work telephone number of a customer or a set of customers
 - *mobile_phone* - The full or partial mobile phone number of a  customer or a set of customers

*Example*

get.customers(account_holder_name='MR TEST ACCOUNT')

*Returns*

customers JSON object

#### contracts

Search EazyCustomerManager for a list of contracts owned by a specific customer.

*Required parameters*

 - *customer* - The GUID of the customer being searched against

*Example*

get.contracts('a1ddc068-51dx-4c6d-bf9c-7866a71c6c43')

*Returns*

contracts JSON object


#### payments

Search EazyCustomerManager for a list of all payments belonging to a specific contract.

*Required parameters*

 - *contract* - The GUID of the contract being searched against


*Optional parameters*

- number_of_rows - The number of payments to return from a contract. By default, this is set to `100`. 

*Example*

get.payments('311228a5-98f5-4bd8-b1b6-023d09ca8b32', number_of_rows='1')

*Returns*

payments JSON object

#### payments_single

Search EazyCustomerManager for a specific payment owned by a specific contract

*Required parameters*

 - *contract* - The GUID of the contract being searched against
 - 'payment' - The GUID of the payment being searched against

*Example*

get.payments('311228a5-98f5-4bd8-b1b6-023d09ca8b32', '13bdf192-86f1-4979-ae00-250a4722e20b')

*Returns*

payment JSON object


#### schedules

Search EazyCustomerManager for all available schedules. **Note:** These can be found without making this call, by  viewing either `sandbox.csv` or `ecm3.csv` in the `.includes` directory.

*Example*

get.schedules()

*Returns*

schedules JSON object


### post

Sends a `POST` request to EazyCustomerManager. Optionally, parameters may be passed. Returns a JSON object or a string.

#### callback_url

Create or update the callback URL used to return data from EazyCustomerManager. **Note:** Although it isn't required, we strongly recommend using a URL secured by the HTTPS protocol. 

`Required parameters`

- *callback_url* - The url of the new location data will be returned to from EazyCustomerManager

*Example*

post.callback_url('https://eazycollect.co.uk')

*Returns*

string - 'The new callback URL is {url}'


#### customer

Create a customer within EazyCustomerManager. **Note**: The SDK will not perform any modulus checks when creating a customer. This is the responsibility of the client, however we can offer a pay-per-use API to achieve this. For more details, contact our sales department via `01242 650052`.

*Required parameters*

- *email* - The email address of the newly created customer. This must be unique.
- *title* - The title of the newly created customer
- *customer_reference* - The customer reference of the newly created customer. This must be unique.
- *first_name* - The first name of the newly created customer  
- surname - The surname of the newly created customer  
- *line1* - The first line of the newly created customers address
- *post_code* - The post code of the newly created customers address
- *account_number* - The bank account number of the newly created customer
- *sort_code* - The sort code of the newly created customer
- *account_holder_name* - The name as it appears on the newly created customers bank account

*Optional parameters*

- *line2* - The second line of the newly created customers address
- *line3* - The third line of the newly created customers address  
- *line4* - The fourth line of the newly created customers address  
- *company_name* - The name of the company the newly created customer represents
- *date_of_birth* - The date of birth of the newly created customer
- *home_phone* - The home phone number of the newly created customer  
- *work_phone* - The work phone number of the newly created customer  
- *mobile_phone* - The mobile phone number of the newly created customer

*Example*

post.customer('test@email.com', 'Mr', 'test-0001', 'Test', 'Ing', '1 Tebbit Mews', 'GL52 2NF', '00000000', '000000', 'MR TEST ING', work_phone='00000000000')

*Returns*

customer JSON object

#### contract

Create a contract within ECM3. It is important to note that due to the complexity of contracts, there are several rules dictating the general flow of contract creation. To aid this, we've created `settings.contracts`, which helps by automatically fixing some of the common issues found when creating contracts.

*Required parameters*

- *customer* - The GUID of the customer the newly created contract will belong to
- *schedule_name* - The name of the schedule the newly created contract will belong to
- *start_date* - The date the newly created contract will start
- *gift_aid* - Whether the newly created contract is eligible for gift aid or not
- *termination_type* - The method of termination for the newly created contract
- *at_the_end* - What happens to the newly created contract after the termination event has been triggered

*Optional parameters*

- *number_of_debits* - Mandatory if `termination_type` is set to `Collect certain number of debits`
- *frequency* - Mandatory if the newly created contract is not ad-hoc. This parameter allows you to skip periods (e.g a value of 2 would collect every 2 months rather than every 1 month)
- *initial_amount* - Called if the first collection amount is different from `payment_amount`. Not to be used on ad-hoc contracts.
- *extra_initial_amount* - Called if any additional charges are present, such as a gym registration fee. Not to be used on ad-hoc contracts.
- *payment_amount* - Mandatory if the contract is not ad-hoc. The regular collection amount for the newly created contract
- final_amount - Used if the final collection amount is different from the rest. Not to be used on ad-hoc contracts.
- *payment_month_in_year* - The collection month for annual contracts. Jan =` 1`, Dec = `12`
- *payment_day_in_month* - The collection day for monthly contracts. Accepts `1`-`28` or `last day of month`
- *payment_day_in_week* - The collection day for weekly contracts. Monday = `1`, Friday = `5`
- *termination_date* - The termination date of the newly created contract. Mandatory if `termination_type` is set to `End on exact date`
- *additional_reference* - An additional reference for the newly created contract.
- *custom_dd_reference* - A custom Direct Debit reference for the newly created contract.

*Example*

post.contract('311228a5-98f5-4bd8-b1b6-023d09ca8b32', 'adhoc_free', '2019-06-15', False, 'Until further notice', 'Switch to further notice')

*Returns*

contract JSON object

#### payment

Create a payment against a contract in EazyCustomerManager.

*Required parameters*

- *contract* - The GUID of the contract the newly created payment will belong to
- *collection_amount* - The total amount the newly created payment will collect
- *collection_date* - The desired collection date date of the newly created payment
- *comment* - A comment to supplement the newly created payment

*Optional parameters*

- *is_credit* - Passed if the payment is a credit to the customer, rather than a debit. **Note:** If the setting `settings.payment['is_credit_allowed']` is set to `False`, this will automatically be set to `False`.


*Example*

post.payment('311228a5-98f5-4bd8-b1b6-023d09ca8b32', '10.50', '2019-06-15', 'Customer needs to pay £10.50')

*Returns*

payment JSON object


### patch

Sends a `PATCH` request to EazyCustomerManager. Optionally, parameters may be passed. Returns a JSON object.

#### customer

Modify a customer within EazyCustomerManager.


*Required parameters*
- `customer` - The GUID of the customer to be modified

*Optional parameters*

- *email* - The new email address of the customer. This must be unique.
- *title* - The new title of the customer
- *date_of_birth* - The amended date of birth of the customer
- *first_name* - The amended first name of the customer  
- *surname* - The amended surname of the customer  
- *line1* - The new first line of the customers address
- *post_code* - The new post code of the customers address
- *account_number* - The new bank account number of the customer
- *sort_code* - The new sort code of the customer
- *account_holder_name* - The amended name as it appears on the customers bank account
- *line2* - The new second line of the customers address
- *line3* - The new third line of the customers address  
- *line4* - The new fourth line of the customers address  
- *company_name* - The name of the new company the customer represents
- *home_phone* - The new home phone number of the customer  
- *work_phone* - The new work phone number of the customer  
- *mobile_phone* - The new mobile phone number of the customer

*Example*

patch.customer('311228a5-98f5-4bd8-b1b6-023d09ca8b32', account_number='00000000', sort_code='000000')

*Returns*

'cusotmer {cusotmer} updated successfully'
