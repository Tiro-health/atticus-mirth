# FhirBundleToKWS

This sample demonstrates how to export reports to KWS. The channel listens for a FHIR bundle, then parses, transforms, and sends it to KWS.

Before importing, ensure you replace all 'FILL ME' placeholders in the `FHIRBundleToKWS.xml` file with the appropriate values, please contact [support@tiro.health](mailto:support@tiro.health) for more information.

## Configuration
This channel works with a Configuration Map (`Settings` → `Configuration Map` tab). The following keys are expected:

| Key                 | Description                                                                 |
|---------------------|-----------------------------------------------------------------------------|
| `tiroKwsAppNaam`    | Application name identifier (Contact Nexushealth)                           |
| `tiroKwsZiekenhuis` | Hospital identifier (Contact Nexushealth)                                   |
| `tiroUsername`      | Authentication username (Contact [support@tiro.health](mailto:support@tiro.health)) |
| `tiroPassword`      | Authentication password (Contact [support@tiro.health](mailto:support@tiro.health)) |

## Test Procedure
1. Run both `mock_kws` and `FhirBundleToKWS` channels
2. In the dashboard, right-click `FhirBundleToKWS` → `Send Message`
3. Click 'Open Text File', select `test_bundle_kws_import.json` and click 'Process Message'
4. Check server logs for generated XML output

## Data Mapping
| Element               | Source                                                                                                |
|-----------------------|-------------------------------------------------------------------------------------------------------|
| eadnr/cnr/externId    | Extracted from `Patient`, `Encounter`, and `QuestionnaireResponse` identifiers                        |
| Verslag text          | Taken from `QuestionnaireResponse.narrative`                                                          |
| Supervisor            | Mapped from `Encounter.participant` field                                                             |
| Arts                  | Not included (assumes pre-existing KWS contact)                                                       |
| Verslag deletion      | Triggered when `QuestionnaireResponse.meta.versionId` indicates a deleted version                     |

---

## Sequence Diagram (Direct Mode)
```mermaid
sequenceDiagram
    actor TiroBackend as Tiro.health Backend
    participant MIRTH as MIRTH_FhirBundleToKWS
    participant KWS as KWS API

    TiroBackend->>MIRTH: POST FHIR Bundle (QuestionnaireResponse + Patient + Encounter)
    activate MIRTH
    Note right of MIRTH: Parse bundle<br/>Extract QR/Patient/Encounter
    
    alt Existing report deletion (versionId check)
        MIRTH->>KWS: MLLP deleteXML (127.0.0.1:2575)
        KWS-->>MIRTH: Deletion confirmation
    end
    
    MIRTH->>KWS: MLLP createXML (127.0.0.1:2575)
    Note right of KWS: Creates "Verslag"<br/>Uses QR.narrative as "Bevinding"
    MIRTH-->>TiroBackend: HTTP 200 OK
    deactivate MIRTH
```

## Sequence Diagram (With Polling)
Instead of a transaction from Tiro.health, you could poll Tiro.health for a QuestionnaireResponse and transform it in to a transaction. The advantage of this approach is that we don't need to setup a VPN to bypass the firewall (the request comes from the hospital). The disadvantage is that the report will not instantly be visible in KWS.

```mermaid
sequenceDiagram
    participant Tiro.health backend
    participant MIRTH_PollForQRs
    participant MIRTH_FhirBundleToKWS
    participant KWS API
    note over MIRTH_PollForQRs: Polls periodically if new reports are submitted in Tiro.health

    loop every minute
    activate MIRTH_PollForQRs

    note over MIRTH_PollForQRs: Source: update ${now} and ${lastNow} to current polling period
    note over MIRTH_PollForQRs: Destination: Get QuestionnaireResponse
    MIRTH_PollForQRs->>Tiro.health backend: GET /fhir/r5/QuestionnaireResponse (poll for recently submitted reports)
    Tiro.health backend-->>MIRTH_PollForQRs: Returns Bundle with QRs (including Patient and Encounters)

        loop For each found QR
            note over MIRTH_PollForQRs: Construct FHIR Bundle (QR, Patient, Encounter)
            MIRTH_PollForQRs->>MIRTH_FhirBundleToKWS: POST http://localhost:8010/ (FHIR Bundle: QuestionnaireResponse, Patient, Encounter)
            deactivate MIRTH_PollForQRs
            activate MIRTH_FhirBundleToKWS
    Note over MIRTH_FhirBundleToKWS: Source Transformer: parse bundle and get first QR, get QR.subject, get QR.encounter 

    opt Optional Delete of Existing Report in KWS (check QR.meta.versionId)
        Note over MIRTH_FhirBundleToKWS: Destination: Optinional delete verslag in KWS 
        MIRTH_FhirBundleToKWS->>KWS API: MLLP 127.0.0.1:2575 (deleteXML)
        Note over KWS API: 'Verslag' deleted in KWS
    end
    Note over MIRTH_FhirBundleToKWS: Destination: Build XML and send to KWS
    MIRTH_FhirBundleToKWS->>+KWS API: MLLP 127.0.0.1:2575 (createXML)
    Note over KWS API: 'Verslag' created with QR.narrative as 'Bevinding'
    activate KWS API
    MIRTH_FhirBundleToKWS-->>Tiro.health backend: HTTP 200 OK
    deactivate MIRTH_FhirBundleToKWS
        end
    end
```