# LeasePlan Assignment

## Assignment Use Case #1

When the customer service agent receives a phone call to book a maintenance check for the
vehicle the agent should be able to list out all the suppliers who provide the maintenance in in the
city where the customer is located and check the location in a map to help the customer book an
appointment.

The Suppliers are stored in an independent custom object in Salesforce.

The Object definition of the custom object is as below:

| Field Label  | Field Name  | Description  |
|---|---|---|
| Name  | Name  | The name of the Supplier.  |
| City  | City__c  | The city in which the supplier is located.  |
| Latitude  | Latitude__c  | The latitude of the location of the Supplier.  |
| Longitude  | Longitude__c  | The Longitude of the location of the Supplier.  |

*Note :* You may also create the flat fields to log the address of the Supplier on the object if
needed instead of geo coordinates.

The customer service agent should be able to search the suppliers that are located on the Billing
city of the Account starting from the Account lightning page to see a list of suppliers and then be
able to drill down to each of the supplier to view the exact location on the Maps.

Take into account that the number of suppliers for a city can at least be more than 100.

## Assignment Use Case #1 Implementation

### Supplier Object

Created the supplier object with additional address fields

| Field Label  | Field Name  | Description  |
|---|---|---|
| Name  | Name  | The name of the Supplier.  |
| Street  | Street__c  | The street in which the supplier is located.  |
| City  | City__c  | The city in which the supplier is located.  |
| Postal Code  | Postal_Code__c  | The postal code for the address in which the supplier is located.  |
| State  | State__c  | The state in which the supplier is located.  |
| Country  | Country__c  | The country in which the supplier is located.  |
| Latitude  | Latitude__c  | The latitude of the location of the Supplier.  |
| Longitude  | Longitude__c  | The Longitude of the location of the Supplier.  |

### Supplier List Component - LWC

This a LWC using `lightning-datatable` to display list of suppliers. This component is a reusable component. Allowed places to use:

* Account Record Page
* Lightning App Pages

When placed on the Account Record page the component will use the record Id to fetch `billingAddress` and by using the City of the `billingAddress` this component will fetch and list all returned suppliers.

The datatable supports *infinite-loading* in case there are big amounth of suppliers.

Each row on this table contains 2 actions:

* Add Action: Add action on this component is a row column button action which publishes a message using LMS to let other components now that the use wants to Add the supplier row to the Supplier Map.
* Create Case: Create Case action is a dropdown button action. If the user (the agent) wants to create a case against a Supplier for the caller customer this action will trigger standard case creation dialog for easy access. The dialog will contains Account and Supplier lookups auto-populated.

![Supplier List](/Assignment/images/listSuppliers.png "List of Suppliers")

### Account Flexipage

![Account page](/Assignment/images/accountPage.png "Account Page")

### Supplier Map Component - LWC

This a LWC using `lightning-map` to display the caller customer and the selected supplier on native Google Maps. This component is a reusable component. Allowed places to use:

* Account Record Page
* Case Record Page (no valid use case)
* Lightning App Pages

In order to display customer marker this component uses the `BillingAddress` information and customer marker will be displayed via star shape.

Selected Supplier markers will be displayed by using their `Address fields`. Geocode fields are *not* in use. Supplier markers will have the default marker shape.

The map contains the map native list view auto enabled.

![Supplier Map](/Assignment/images/supplierMap.png "Map of Suppliers")

### Maintanence Scheduler Lighnint App - out of requested scope

This is a Lightning Application created to enable the service agent to work on a single page instead of navigating between Account, Case , Supplier objects.

![Maintenance Scheduler](/Assignment/images/maintenanceScheduler.png "Maintenance Scheduler")

The page comes with a customer search component where the service agent can search for a customer via their name or via their phone number. Partial search is allowed for Name but Phone excepts exact match due to security and scalability concerns.

When there are more than one matching customer returned after searching with partial name term the component will display all matching customers in a datatable for service agent to verify and select.

![Maintenance Scheduler multiple matches](/Assignment/images/multipleMatches.png "Maintenance Scheduler multiple matches")

*Note:* This component doesn't support infinite-loading. Performance aspect is ignored for this assignment.

Selecting a client will publish a message thru LMS for Supplier List and Supplier Map components to get notified and act accordingly.

![Maintenance Scheduler with Customer](/Assignment/images/maintenanceSchedulerMarkers.png "Maintenance Scheduler with Customer")

## Assignment Use Case #2

The customer service agent logs the satisfaction rating of the customer after the resolution of a
case in a field on the Case object.
The rating is logged with values between 1 and 5. 5 being a highly satisfied customer.

The average rating of all the cases of an Account needs to be visible on a field on the Account
record so that the agents have an overview of customer satisfaction.

The Average customer satisfaction rating needs to be updated on the Account on closure of each
case.

*Note :* In your design and code take into consideration that an account can have a large number
of cases logged.

## Assignment Use Case #2 Implementation

### Standard Object Changes

* In order to store rating there is new field on the Case object "Rate (Rate__c)"
* In order to store calculated average rating there is new field on the Account object "Average Rate (Average_Rate__c)"

### Case Rating - LWC

This is a LWC to receive and display star rating for Case and Account object. The component has static (read-only) and interactive modes.
Static mode is used on the Account object 

![Account rating](/Assignment/images/accountRating.png "Account average case rating")

and the interactive mode is used on the Case object pages.

![Case rating](/Assignment/images/caseRating.png "Case rating")

Considering the usage is different on these object the title is also different and it is configurable. The configuration of the component can be seen below.

![caseRating config](/Assignment/images/ratingConfig.png "Case Rating configurations")