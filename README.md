# Atticus MIRTH Sample Configurations

In this repo, you can find a number of [MIRTH](https://www.nextgen.com/solutions/interoperability/mirth-integration-engine) sample channels to connect with Atticus.

## Data & Context Integration

An integration typically consists of three phases:
- A data import phase, where data is retrieved from a source system and loaded into Atticus. The data is mostly metadata about the patient, the current encounter and the report that must be completed.
- A context launch phase, where user opens the application on a specific patient and encounter. This is typically done by integrating a parametrized URL into the source system, which opens Atticus in the context of the patient and encounter.
- A data export phase, where data is sent back to the source system after the user has completed the report in Atticus. This is typically done by sending a FHIR QuestionnaireResponse resource back to the source system.


# Available Channels
This repository contains multiple MIRTH channels that demonstrate how to integrate with Atticus. Although there are multiple channels for import and export, you only need one of each phase to get started. The channels are organized as follows:


- [`channels/`](channels/): Contains the sample channel files for MIRTH.
  - [`import/`](channels/import/): Contains the channel files responsible for importing data into Atticus.
    - [`HL7v2`](channels/import/HL7v2/): Channel that converts HL7v2 messages into a FHIR transaction to import data into Atticus.
    - [`URL`](channels/import/URL/): Channel that converts a parametrized URL into a FHIR transaction to import data into Atticus.
  - [`export/`](channels/export/): Contains the channel files responsible for exporting data from Atticus.
    - [`PollForQRs`](channels/export/PollForQRs/): Channel that repeadetly polls for FHIR QuestionnaireResponses in Atticus and routes them to another channel.

## Setup

### Downloading MIRTH

You can download MIRTH from the [NextGen Repo](https://github.com/nextgenhealthcare/connect/releases). Follow the instructions on the website to install MIRTH on your system.

### Importing and Starting a Channel

1. Open the MIRTH Connect Administrator.
2. Go to the "Channels" tab.
3. Click on the "Import Channel" button.
4. Select the channel file you want to import from this repository.
5. Once the channel is imported, select it from the list and click on the "Deploy Channel" button to start it.

For more detailed instructions, refer to the [MIRTH Connect User Guide](https://docs.nextgen.com/category/mirth_user_guides).
