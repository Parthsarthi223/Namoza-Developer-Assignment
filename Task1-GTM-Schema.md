Task 1 – GTM Event Schema
1. Complete GTM Event Schema
Event Name	Trigger Type	Key Parameters (Minimum 3)	GA4 Report / Audience
booking_step_complete	Custom Event (Data Layer)	step_number, step_name, clinic_location, specialty	Funnel Exploration
booking_completed	Custom Event (Data Layer)	booking_id, clinic_location, specialty, appointment_date	Conversions Report
consultation_form_start	Form Interaction	page_location, device_type, traffic_source	Funnel Exploration
consultation_form_submitted	Form Submission	name, phone, source	Google Ads Conversion & Conversions Report
call_now_click	Link Click (tel:)	page_location, clinic_location, button_position	Engagement Report
whatsapp_click	Link Click (wa.me)	page_location, clinic_location, device_type	Audience: WhatsApp Users
patient_guide_download	Form Submit + PDF Download	patient_name, phone_number, guide_name	Lead Generation Report
clinic_page_view	Page View	clinic_location, page_title, page_url	Landing Page Report
blog_scroll_50	Scroll Depth Trigger (50%)	article_title, category, scroll_percent	Engagement Report
blog_scroll_90	Scroll Depth Trigger (90%)	article_title, category, scroll_percent	Engaged Readers Audience
outbound_link_click	Link Click	destination_url, link_text, page_location	Engagement Report
page_view	Page View	page_location, page_title, traffic_source	Standard GA4 Reports

2. Tracking the 3-Step Booking Form
The booking form consists of three steps:
1.	Select Clinic Location & Specialty 
2.	Enter Personal Details 
3.	Confirm Booking 
Since GTM cannot automatically detect changes inside a custom multi-step form, the front-end developer must implement window.dataLayer.push() events whenever a user successfully completes each step. GTM will listen for these custom events and forward them to GA4.

Step 1 – Select Clinic & Specialty
GTM Trigger
Trigger Type: Custom Event
Event Name: booking_step_complete
Condition: step_number = 1
dataLayer Push
{
  "event": "booking_step_complete",
  "step_number": 1,
  "step_name": "location_specialty_selected",
  "clinic_location": "Indiranagar",
  "specialty": "Knee Replacement",
  "page_location": "/book-consultation"
}

Step 2 – Enter Personal Details
GTM Trigger
Trigger Type: Custom Event
Event Name: booking_step_complete
Condition: step_number = 2
dataLayer Push
{
  "event": "booking_step_complete",
  "step_number": 2,
  "step_name": "patient_details_entered",
  "patient_name": "Rahul Sharma",
  "phone": "9876543210",
  "preferred_date": "2026-07-10",
  "clinic_location": "Indiranagar"
}

Step 3 – Confirm Booking
GTM Trigger
Trigger Type: Custom Event
Event Name: booking_completed
dataLayer Push
{
  "event": "booking_completed",
  "step_number": 3,
  "step_name": "booking_confirmed",
  "booking_id": "ORTHO-45872",
  "clinic_location": "Indiranagar",
  "specialty": "Knee Replacement",
  "appointment_date": "2026-07-10"
}

3. GTM Trigger Configuration
Booking Step	Trigger Type	Event
Step 1	Custom Event	booking_step_complete (step_number = 1)
Step 2	Custom Event	booking_step_complete (step_number = 2)
Step 3	Custom Event	booking_completed

4. GA4 Funnel Exploration Setup
To measure user drop-off between booking steps, a Funnel Exploration report will be created in GA4 with the following sequence:
Step 1
•	Event Name = booking_step_complete 
•	Parameter = step_number = 1 
↓
Step 2
•	Event Name = booking_step_complete 
•	Parameter = step_number = 2 
↓
Step 3
•	Event Name = booking_completed 
This funnel provides the following insights:
•	Number of users starting the booking process. 
•	Percentage of users abandoning after Step 1. 
•	Percentage abandoning after Step 2. 
•	Overall booking completion rate. 
•	Highest drop-off stage for optimization. 
•	Performance comparison across clinic locations and specialties. 

5. Google Ads Conversion to Import
Recommended Conversion Event:
booking_completed
Why choose this event?
The booking_completed event represents the final business objective—a successfully booked consultation. Importing this event into Google Ads enables Smart Bidding strategies such as Target CPA or Maximize Conversions to optimize campaigns based on actual appointment bookings rather than intermediate actions like button clicks or form starts.
Although events such as call_now_click, whatsapp_click, and consultation_form_start indicate user engagement, they do not guarantee a qualified lead. Optimizing Google Ads using the booking_completed conversion ensures advertising spend is focused on generating real patient appointments, resulting in higher-quality leads and improved return on ad spend (ROAS).

6. Front-End Developer Implementation Note
Google Tag Manager cannot automatically detect internal transitions within a custom multi-step booking form. The front-end developer is responsible for triggering the appropriate window.dataLayer.push() event after each step is successfully completed.
For example, when the user completes Step 2 (Patient Details), the developer should execute:
window.dataLayer = window.dataLayer || [];

window.dataLayer.push({
    event: "booking_step_complete",
    step_number: 2,
    step_name: "patient_details_entered",
    patient_name: patientName,
    phone: phoneNumber,
    preferred_date: appointmentDate,
    clinic_location: selectedClinic
});
Once this event is pushed into the Data Layer, GTM captures it using a Custom Event Trigger and sends it to GA4. This approach ensures accurate step-level tracking, enables Funnel Exploration reporting, and allows the marketing team to identify where users abandon the booking process.

