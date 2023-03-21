# TradeTracker.com Google Tag Manager Template

This tag template makes it easy for advertisers to add TradeTracker.com's conversion pixel to Google Tag Manager container. If you have any questions or need any assistance please contact your Account Manager, or email at support@tradetracker.com.

### Contents: 
* [Configuring your Tag](#config)
* [Before setting up your tag](#preparation)
  * [Sales Tag Variables & Triggers](#sales)
  * [Leads Tag Variables & Triggers](#leads)
  * [Multi Product-Group Sales Tag Variables and Triggers](#multi)
* [Other Tag Types](#other)

----

## <a name="config"></a>Configuring your Tag

1. Download the the .zip of this repository and extract (unzip) the archive  
1. Create a new template in Google Tag Manager and import the template.tpl file from the zip using the overflow menu at the top right
1. Add a new tag, search for the TradeTracker.com tag custom template and select it
1. Select a Tag Type from the drop-down menu
1. Add your Campaign Id as text or enter the variable name
1. Add your ProductGroup Id as text or enter the variable name
1. Enter the variable name for your Transaction  Id variable
1. Enter the variable name for your Transaction Amount variable ('Sales' tag type only)
1. Select the correct firing trigger for the tag
1. Save the tag


## <a name="preparation"></a>Before setting up your TradeTracker.com Tag

Make sure you have a **Campaign Id** and **ProductGroup Id** that have been provided by your Account Manager at TradeTracker.com. You'll need to enter these during the tag configuration.

You'll also need to ensure that you have GTM (Google Tag Manager) Variables configured that capture the data that you want to track, and GTM Triggers to fire the tag on these events. 

## <a name="sales"></a>Sales Tag Variables & Triggers
If you want to track retail sales from your website you will need to capture the transaction (order) details, and fire this tag on the purchase event. For this we highly recommend using Google's [Enhanced Ecommerce](https://developers.google.com/tag-manager/enhanced-ecommerce#purchases) model for pushing purchase events to the dataLayer. We require these two specific variables:

### <a name="tran"></a>Trasaction ID:
This variable should capture the unique Transaction or Order ID for the sale. This should be the same ID that you use to track your sales internally, and it's important that it's unique for every sale. 

In the Enhanced Ecommerce purchase model this variable can be found in the dataLayer at `ecommerce.purchase.actionField.id`. If you need to create new GTM variable for this value, create a new dataLayer type variable, and enter this address in the "Data Layer Variable Name" field.


### Transaction Amount:
This variable should capture the total monetary amount for the sale (sum of all included items) **excluding** any applicable tax such as VAT. It should be formatted as a "string", and should be a dot-decimal number to two decimal places. 

Examples: 
```
Sale amount:      $9.81 => "9.81"
Sale amount: € 2.145,99 => "2145.99"
```

The transaction amount cannot be taken directly from the Enhanced Ecommerce purchase object, as the "revenue" variable typically contains the total amount *including* tax. 

The recommended way to capture this amount is with a "Custom JavaScript Variable" in GTM. You will first need to create dataLayer variables for the transaction revenue (`ecommerce.purchase.actionField.revenue`) and transaction tax (`ecommerce.purchase.actionField.tax`) amounts.

Once this is done you can then create a new Custom JavaScipt Variable that should look something like this: 

```javascript
function () {
  return ({{your_transaction_revenue_variable}} - {{your_transaction_tax_variable}}).toFixed(2)
}
```

### Purchase Trigger:
This GTM Trigger should be set to fire either when a "purchase" event is pushed to the dataLayer, or to match the url of your post-purchase "thank-you" page and fire the tag there. 

Firing on the purchase event is a more reliable way to fire the tag, and if you use the Enhanced Ecommerce purchase model you can create a "Custom Event" trigger, and enter `purchase` as the "Event name" to create this trigger.

Alternatively if you need to create a trigger based on the thank-you page url or another event, test it thoroughly to make sure that it fires reliably and that the transaction detail variables will be avialable to the tag when the trigger fires it.

----


## <a name="leads"></a>Leads Tag Variables and Triggers
If you want to track custom leads events such as email signups or demo requests, you will need to create some custom variables to provide to the TradeTracker.com tag. Due to the highly customizable nature of these events, we cannot offer as much advice as we can for the sales tag, but we do have a few guidelines.

### Transaction ID: 
The Leads tag still requires a Transaction Id. In this case the transaction id tracks a unique user event on your website. If this will track something like email signups where an id is returned from the submitted form, you could provide this id so that these leads events can be matched from TradeTracker.com's tracking to your own analytics. 

If you are tracking an event that does not have an provide it's own id, you could create a unique identifier for events by combining a random number and a timestamp in a GTM Custom JavaScript variable. For example: 

```javascript
function () {
  return Date.now() + Math.random() 
}
```

### Event Trigger
The trigger for your leads tag will be defined by the type of user event that you wish to track. If the event includes a click on a particular button, or a post-signup page you can create GTM triggers for these events. Alternatively you may want to look at creating a custom event trigger. More information on how to set up these triggers can be found on Google's [Trigger types](https://support.google.com/tagmanager/topic/7679108?hl=en&ref_topic=7679384) article.

----

## <a name="multi"></a>Multi Product-Group Sales Tag Variables and Triggers
If you run a campaign that makes use of multiple 'product-groups' to assign different commission rates to different types of products, you can select the "Sales - Multi Product Group" option from the Tag Type dropdown menu when configuring your tag. 

In order to support this type of Sales tag you will need to provide the basket items (purchased products) to the dataLayer. For this we highly recommend using Google's Enhanced Ecommerce model for pushing purchase events to the dataLayer. We require these two specific variables, and a list of product-group ids to check for.

### Trasaction ID:
This is the same Transaction Id that you would use in the normal Sales tag. It should be a unique identifier that you use to track your sales internally. More details can be found under the [Sales Tag section](#trans).

### Default Product-Group ID:
This should be the default product-group id (provided by your TradeTracker.com Account Manager) that the tag will use if it cannot find a matching product-group for a product's category.
You can enter this as text in the field, or you could create a GTM "Constant" variable type and enter the name of that variable.

### Basket Items Array:
In order to use this tag type without customization you will need to use Google's [Enhanced Ecommerce](https://developers.google.com/tag-manager/enhanced-ecommerce#purchases) model for pushing purchase events and related data to the dataLayer. 

Once this is configured, you will need to create a GTM "dataLayer" type variable, and use the dataLayer location `ecommerce.purchase.products`. This will provide the TradeTracker.com tag with the basket items, their prices and categories so that we can fire the tracking tag with the appropriate product-group ids.

If the Enhanced Ecommerce style products array is not available, you can create your own GTM variable to fill this data using the following schema below. It should be an array of objects, and each object needs the keys "price", "quantity" and "category". Any other product specific keys like the product name, EAN or identifier can be dropped. 

```json
[
  {
    "price": 12.34,
    "quantity": 1,
    "category": "category / sub-category"
  },
  {
    "price": 98.76,
    "quantity": 3,
    "category": "category / sub-category"
  }
]
```

### Basket Items Array:
This drop down allows you to select a DataLayer model. Either the [Universal Analytics Enhanced Ecommerce Purchase event](https://developers.google.com/analytics/devguides/collection/ua/gtm/enhanced-ecommerce?hl=en#purchases), or the [Google Analytics 4 Purchase event](https://developers.google.com/analytics/devguides/collection/ga4/set-up-ecommerce?hl=en). 

### Additional Product Groups
This field allows you to add additional product-group ids and the category keywords that should be used to match products to that product-group. 

You can add an additional product-group using the "Add Row" button. Enter the product-group id on the left, and then category keyword(s) in the field on the right. If you need to enter multiple keywords for a single product-group id, you can separate these with a comma. 

For Example:
```
Product-Group Id  |  Category Keywords
--------------------------------------------------
12345             | Sun Glasses, Eyewear, Contacts
23456             | Shirts, Sweaters, Jumpers
34567             | Jeans
```

It is also possible to use regular expressions in the Category Keywords field. If for example you use the category keyword `glasses`, this will find matches in the categories "Glasses", and "Sunglasses". To make this more strict, you could instead enter the regular expression `^glasses$` to ensure that only "Glasses" is matched with that product-group. 

<em>Matching Category Keywords to Product-Group Ids will require some testing. How you structure your category paths internally will affect how they are pushed to the dataLayer, and how our Tag searches those categories for keywords. If you need any additional information or support on configuring this, please reach out to your Account Manager or contact us using support@tradetracker.com

## <a name="fallback"></a>Sales tag Fallback

In the event that the tag cannot construct a valid basket or list of product groups it will fallback to executing a regular sale pixel call to ensure that the transaction is still recorded.


## <a name="other"></a>Other Tag Types:
This Google Tag Manager template only supports the most common tracking scenarios for TradeTracker.com marketing campaigns. If you run a more complex performance marketing campaign that makes use of multiple campaigns for different brands, Exclusive Voucher Codes or needs to support multiple countries, please get in touch with your TradeTracker.com Account Manager for further support and guidance on how implement our tracking. If you're unsure of how to contact your Account Manager, please email us at support@tradetracker.com

