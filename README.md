# Polarity Detection Forms Integration

The Polarity Detection Forms integration enables users to submit form-based detection feedback or requests via email.  The integration can
be easily customized with your own forms by providing custom form configurations.

| ![](images/NewRuleNomination.png) | ![](/images/ExistingRuleFeedback.png) | ![](images/ExistingRuleFeedback_byREntity.png)
|------------------------|----------------------|-------------------------|
| *New Rule Nomination*  | *Existing Rule Feedback*   | *Existing Rule Feedback from Rule Number*  |

# Data

When a form is submitted by a user, the submitted form will include the following information:

* Username
* User Email
* User FullName
* Integrations that returned results for the user including summary tags (if enabled)
* Custom Form Data

# Integration Options

### Form Delivery Method
Choose how form data should be delivered. The default is "Email".

* Email -- form data will be sent to the configured email address (SMTP settings must be configured)
* Log File -- form data will be logged as JSON in the integration log file
* Email and Log File -- form data will be sent via email and logged (SMTP settings must be configured)

### SMTP Host

Your SMTP server hostname.

### SMTP Server Port

Your SMTP server port.

### SMTP User

Your SMTP username. Leave blank for unauthenticated SMTP servers.

### SMTP Server Password

Your SMTP password. Leave blank for unauthenticated SMTP servers.

### SMTP Connection Security

The type of connection security to use when connecting to the SMTP server.

Options are:

* Default -- STARTTLS is used if it's supported by the SMTP Server.
* STARTTLS -- Forces the use of STARTTLS even if the SMTP server does not advertise it as being supported (the connection will fail on SMTP servers that cannot upgrade the connection).
* TLS -- Forces the use of TLS

### Default Recipient Email Address

The default email address to send form submissions to if a form configuration does not specify a recipient.

### Enabled Forms

A comma delimited list of forms that should be available when interacting with this integration.  Forms should be referred to by their filename (not including the `.json` file extension).

### Include Integration Names and Summary Tags

If checked, the email will include the name of integrations the user had access to when submitting the form as well as summary tags.

### Summary Tag

Customize the integration's summary tag.  The default is `Feedback`.

### Show Forms by Default

If checked, the integration will show the forms by default.  If unchecked, the user will need to click to expand and view the forms.

# Adding forms

You can add a form by creating your own form configuration file (`<your-form>.json`). Save the form in the integration's `forms` directory and restart the integration from Integration settings page.  Once the integration has been restarted, you can update the

# Creating your own forms

The integration supports creating your own forms via `json` configuration files.  Each form is defined in a separate
configuration file saved in the integration's `forms` directory.

A configuration file has the following properties:

**name** {string} _(Required)_ - The name of the form

**recipient** {string | string[]} _(Optional)_ - The email address to send the form to.  If not specified the form will be sent to the integration's default recipient.  If a list of recipients is provided, the user will be allowed to choose the recipient using a drop menu in the form.

**recipientDomains** {string[]} _(Optional)_ - If specified the user will need to provide their own recipient email address.  To prevent any email from being entered, the recipient will only be allowed to send emails to the specified domains.

**subject** {string} _(Optional)_ - The subject of the email.  If not specified the subject will be "Polarity Form Submission".

**description** {string} _(Optional)_ - A description of the form

**elements** {array} _(Required)_ - An array of form elements

The following is an example form with a single `textarea` element specified:

```
{
  "name": "Support Request",
  "recipient": "support@company.internal",
  "description": "Send us a support request",
  "elements": [
    {
      "type": "textarea",
      "label": "Support Details",
      "placeholder": "Provide details about your request",
      "required": true
    }
  ]
}
```

## Recipients

The recipient of the form email can be specified in multiple ways.  Be default, if no `recipient` is specified for the form, then the integration's default recipient will be used which is set on the Integration Settings page.  This is an integration wide recipient that can be applied to all forms.

### Single Recipient

To provide more flexibility, you can also specify a recipient on a per-form basis.  As an example, the following form will always be sent to `support@company.internal`:

```
  "name": "Support Request",
  "recipient": "support@company.internal",
  "description": "Send us a support request",
  "elements": [
    {
      "type": "textarea",
      "label": "Support Details",
      "placeholder": "Provide details about your request",
      "required": true
    }
  ]
}
```

### Multiple Recipients

If you would like the user to be able to choose from a list of recipients you can provide a list of recipient emails using the `recipient` property.  In this example, the user can choose which team to send the form to by selecting from a Recipient drop down list:

```
{
  "name": "Support Request",
  "recipient": ["team1@company.internal", "team2@company.internal", "team3@company.internal"],
  "description": "Send us a support request",
  "elements": [
    {
      "type": "textarea",
      "label": "Support Details",
      "placeholder": "Provide details about your request",
      "required": true
    }
  ]
}
```

### User provided recipient

Finally, you can allow users to provide their own recipient email address but restrict which domains the email can include.  To do this, provide a list of allowed domains using the `recipientDomains` property:

```
{
  "name": "Support Request",
  "recipientDomains": ["company.internal", "us.company.internal", "eu.company.internal"],
  "description": "Send us a support request",
  "elements": [
    {
      "type": "textarea",
      "label": "Support Details",
      "placeholder": "Provide details about your request",
      "required": true
    }
  ]
}
```

In the above example, the user will be asked to provide the email address to send the form to but the email address must pick from one of the three provided domains.


## Form Elements

Each form is made up of one or more Form Elements which are specified within the `elements` array of the form configuration.  The following form elements are supported:

* input -- A text input box
* select -- A drop-down select box
* textarea -- A multi-line text input box
* links -- a list of links
* block -- a block of text to include HTML

### input

The `input` form element renders an HTML input.  An `input` form element has the following properties:

**type** {string} _(required)_ - The type of form element (must be `input`)

**label** {string} _(required)_ - The label (or name) of the input

**default** {string} _(default: "")_ - The default value of the input

**required** {boolean} _(default: false)_ - Whether the input is required for the form to be submitted

**placeholder** {string} _(default: "")_ - A short description of what the input is for.  This is displayed to the user when the input is empty.  For longer instructions we recommend using the `description` property.

**description** {string} _(default: "")_ - A longer description of what the input is for.  This is displayed below the input.

Example `input` form element with all options specified:

```
{
  "type": "input",
  "label": "Subject",
  "required": true,
  "default": "My Subject",
  "placeholder": "Short description of the issue",
  "description": "Provide a short description of the issue you are experiencing"
}
```
### textarea

The `textarea` form element displays an HTML text area and is useful for gathering larger amounts of user input.  The `textarea` element has the following properties:


**type** {string} _(required)_ - The type of form element (must be `textarea`)

**label** {string} _(required)_ - The label (or name) of the textarea

**default** {string} _(default: "")_ - The default value of the textarea

**required** {boolean} _(default: false)_ - Whether the textarea is required for the form to be submitted

**placeholder** {string} _(default: "")_ - A short description of what the textarea is for.  This is displayed to the user when the input is empty.  For longer instructions we recommend using the `description` property.

**description** {string} _(default: "")_ - A longer description of what the textarea is for.  This is displayed below the textarea.

**rows** {number} _(default: 10)_ - The number of rows of text to display in the textarea


Example `textarea` form element:

```
{
  "type": "textarea",
  "label": "Description",
  "required": true,
  "placeholder": "Detailed description of the issue",
  "description": "Provide a detailed description of the issue you are experiencing",
  "rows": 5
}
```

### select

The `select` form element display a dropdown menu with 1 or more options to choose from.  The `select` form element has the following properties:


**type** {string} _(required)_ - The type of form element (must be `select`)

**label** {string} _(required)_ - The label (or name) of the select box

**default** {string} _(default: "")_ - The default value of the select box

**required** {boolean} _(default: false)_ - Whether the select box is required for the form to be submitted

**description** {string} _(default: "")_ - A longer description of what the select box is for.  This is displayed below the select box.

**values** {array} _(required)_ - An array of options to display in the select box


Example `select` form element with all options specified:

```
{
  "type": "select",
  "label": "Severity",
  "required": true,
  "description": "Select the severity of the issue you are experiencing",
  "values": [
    "Low - 24 Hours",
    "Medium - 8 hours",
    "High - 4 hours",
    "Urgent - 1 hour"
  ],
  "default": "Low - 24 Hours"
}
```

### links

The `links` form element is used to display link information to the user.  The `links` form element has the following attributes:


**type** {string} _(required)_ - The type of form element (must be `links`)

**label** {string} - The label for the links

**links** {array} _(required)_ - An array of link objects to display.

**description** {string} _(default: "")_ - An optional description for the links


A link object can contain the following attributes:

```
text {string} - The text to display for the link.  If not provided, the link will display the `href` value.
href {string} - The URL to link to (supports mailto: links)
afterIcon {string} - The name of a Font Awesome icon to display after the link
beforeIcon  {string} - The name of a Font Awesome icon to display before the link
```

Example `links` form element with all options specified:

```
{
  "type": "links",
  "label": "Useful Links",
  "description": "Here are some useful links",
  "links": [
    {
      "text": "Open Google",
      "href": "https://www.google.com",
      "afterIcon": "external-link-alt"
    },
    {
      "text": "Send us an email",
      "href": "mailto:support@customer.internal",
      "beforeIcon": "envelope"
    }
  ]
}
```

### block

The `block` form element is used to display general information to the user and is not an interactive form element.  The `block` form element has the following attributes:

**type** {string} _(required)_ - The type of form element (must be `block`)

**label** {string} - The label (or name) of the block

**text** {string} _(required)_ - The text to display in the block (also supports html)

**description** {string} _(default: "")_ - An optional description for block

Example `block` form element:

```
{
  "type": "block",
  "label": "Block of Text",
  "text": "This is a block of text that can contain <b>html</b>"
}
```

## About Polarity

Polarity is a memory-augmentation platform that improves and accelerates analyst decision making. For more information
about the Polarity platform please see:

https://polarity.io/
