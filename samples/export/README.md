# PollForQRs

This sample demonstrates how to poll for any reports that have been submitted in Atticus.

This channel polls every minute and does a FHIR search query to get all QRs that are completed in the current polling interval:<br>
<code>GET /fhir/r5/QuestionnaireResponse?_lastUpdated=ge[X]&status=completed&_include=subject&_include=encounter</code>

For every QuestionnaireResponse that was found, we create a collection bundle with the QuestionnaireResponse, Patient and Encounter and route it to another channel.

Before importing, ensure you replace all 'FILL ME' placeholders in the `PollFOrQRs.xml` file with the appropriate values. For the necessary credentials, please contact [support@tiro.health](mailto:support@tiro.health).
