# URL to FHIR Transaction

This sample demonstrates how to transform a URL into a FHIR transaction and import it into Atticus. The channel monitors GETs to [http://localhost:80/launch?patient_id=123](http://localhost:80/launch?patient_id=123) and sends a POST request to the [Atticus Transaction API](https://docs.tiro.health/fhir/Transaction). If the POST is a success, the browser is redirected to Atticus home page. If not, the browser is redirected to an error screen.

Before importing, ensure you replace all 'FILL ME' placeholders in the `LaunchURLToFhir.xml` file with the appropriate values. For the necessary credentials, please contact [support@tiro.health](mailto:support@tiro.health).

## Custom Mappings
In the source transformer you can map URL query parameters to import fields in Atticus. Currently, a mapping is provided for patient_id and encounter_id