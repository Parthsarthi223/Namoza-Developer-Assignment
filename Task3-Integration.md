Integration Architecture

When a user submits the consultation form, the landing page first validates the input (Name, Phone, and Clinic Preference) using JavaScript. After successful validation, a window.dataLayer.push() event named consultation_form_submitted is triggered. Google Tag Manager (GTM) listens for this event and sends it to Google Analytics 4 (GA4). The same event is marked as a conversion and imported into Google Ads so that campaigns can optimize for completed consultation requests.

Instead of using HubSpot Forms, I would use HubSpot CRM Contacts API because the landing page is custom-built and requires more flexibility. The form data is sent from the landing page to a secure backend API. The backend first searches HubSpot for an existing contact using the phone number, since HubSpot's default deduplication works on email rather than phone. If a contact with the same phone number exists, the backend updates the existing record; otherwise, it creates a new contact with the following properties:

Name
Phone Number
Clinic Preference
Source = "Google Ads - Consultation Landing Page"
Lead Status = "New Enquiry"

After the HubSpot operation is successful, the backend sends a request to Karix WhatsApp Business API to deliver a confirmation message to the patient within two minutes.

Biggest Failure Point

The biggest failure point is contact deduplication. Since HubSpot does not automatically deduplicate contacts based on phone numbers, duplicate records could be created if this is not handled properly. To avoid this, the backend should always search for an existing contact using the phone number before creating a new one. This ensures data consistency and prevents duplicate patient records.

WhatsApp SLA Monitoring

The WhatsApp confirmation message must be delivered within two minutes. Delays can occur due to API failures, network issues, or Karix service outages. To maintain the SLA, the backend should implement retry logic with exponential backoff, log all API requests and responses, and monitor delivery status. If a message is not delivered within the expected time, an alert should be generated through a monitoring system (such as CloudWatch, Datadog, or Grafana), allowing the support team to investigate and resolve the issue quickly.