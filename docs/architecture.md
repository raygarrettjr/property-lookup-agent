# Property Lookup Agent — System Architecture

## Purpose
The Property Lookup Agent provides a fast, structured way to retrieve parcel, owner, and permit information for any Wake County property using a simple address‑based search with autocomplete. The system is intentionally lightweight, using static datasets, a stateless n8n workflow, and a GitHub Pages front‑end.

## High‑Level Flow
1. The user enters a street number.
2. The user begins typing a street name; the front‑end filters `addresses.json` to show matching addresses.
3. The user selects an address from the dropdown.
4. The front‑end retrieves the associated PIN and sends it to the n8n webhook endpoint.
5. n8n loads the parcels, owners, and permits datasets.
6. The workflow joins records on PIN and assembles a sectioned HTML response.
7. The formatted result is returned to the front‑end and displayed to the user.

## Data Model
The system uses three normalized datasets:

- **parcels.csv** — physical characteristics, valuation, and structured address fields  
- **owners.csv** — ownership, mailing address, last sale date, last sale price  
- **permits.csv** — permit activity for the last 5 years  

The parcels table includes structured address components:

- STREET_NUMBER  
- STREET_NAME  
- SITE_ADDRESS  
- CITY_DECODE  
- STATE  
- ZIPNUM  

This structure supports both fast autocomplete filtering and clean, canonical address formatting in the final output.

## Workflow Architecture
The n8n workflow is a stateless API endpoint that performs:

- Receive PIN from the front‑end  
- Load CSV datasets from the repository  
- Match parcel, owner, and permit records using PIN  
- Format the response into a sectioned HTML block  
- Return JSON or HTML to the front‑end  

This design keeps the backend simple, portable, and easy to extend.

## Front‑End Architecture
The GitHub Pages front‑end performs:

- Load `addresses.json` at page load  
- Filter addresses by street number and street name as the user types  
- Display an autocomplete dropdown of matching addresses  
- Send the selected PIN to the n8n webhook  
- Render the returned HTML inside a results container  

Because all filtering happens client‑side, the UI is fast and requires no server infrastructure.

## Repository Structure
