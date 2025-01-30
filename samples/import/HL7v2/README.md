# HL7v2 to FHIR Transaction

This sample demonstrates how to transform an HL7v2 message into a FHIR transaction and import it into Atticus. The channel monitors a directory for `.hl7` files and sends a POST request to the [Atticus Transaction API](https://docs.tiro.health/fhir/Transaction).

Before importing, ensure you replace all 'FILL ME' placeholders in the `HL7v2ToFHIR.xml` file with the appropriate values. For the necessary credentials, please contact [support@tiro.health](mailto:support@tiro.health).
