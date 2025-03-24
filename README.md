# Example Group

This is an example group that demonstrates how to structure content for an Our Area community website like Our Oakley. It shows how to organize events, venues, and organisers with their associated information.

## Directory Structure

```
example-group/
├── content/
│   ├── events/              # Event content organized by date
│   │   └── event-name       # A directory per event
│   ├── organisers-info/     # Detailed information about organisers
│   └── venues-info/         # Detailed information about venues
├── static/
│   └── admin/             # Decap CMS configuration
└── README.md
```

## Content Types

### Events
- Located in `content/events/`
- Can be organized in any way preferred
- Can be associated with multiple venues and organisers
- Each event has:
  - Title
  - Description
  - Start/End dates
  - Venues (taxonomy from main site)
  - Organisers (taxonomy from main site)
  - Content body

### Venues Info
- Located in `content/venues-info/`
- Flat structure (no nesting)
- Contains detailed information about venues
- Allows venue info to be updated
- Each venue info has:
  - Venue (matches taxonomy term that this relates to)
  - Title
  - Description
  - Content body
  - Build settings (hidden in CMS)

### Organisers Info
- Located in `content/organisers-info/`
- Flat structure (no nesting)
- Contains detailed information about organisers
- Each organiser info has:
  - Organiser (matches taxonomy term that this relates to)
  - Title
  - Description
  - Content body
  - Build settings (hidden in CMS)

## Setup Process

1. Create the directory structure:
   ```bash
   mkdir -p content/{events,organisers-info,venues-info}
   mkdir -p static/admin
   ```

2. Copy the Decap CMS configuration:
   ```bash
   cp config.yml static/admin/
   ```

3. Create initial content:
   - Create an example event in `content/events/`
   - Use at least one venue in event
   - Use at least one organiser in event
   - Create corresponding info pages in `venues-info/` and `organisers-info/`

4. Configure build settings:
   - The `venues-info` and `organisers-info` collections have build settings configured in the CMS
   - These settings ensure the info pages are not rendered but are available for listing
   - The settings are managed through the CMS and don't need manual configuration

## Usage

1. Events can be created in the CMS and will be automatically organized by date when published
2. When creating an event, you can:
   - Enter venues and organisers as needed
   - It is important to match existing venues and organiser to ensure they link correctly
   - Add new venues and organisers as needed
   - Add additional information in the relevant info collections as needed (if you are the organiser/venue)
3. Venues and organisers can be managed independently
4. Detailed information about venues and organisers is stored in the info collections
5. The info collections are not rendered as pages but are available for listing and reference

## Build Settings

The info collections (`venues-info` and `organisers-info`) have specific build settings:
- `render: "never"` - Pages are not rendered as HTML
- `list: "always"` - Pages are included in collections
- `publishResources: true` - Resources are published

These settings ensure that the info pages are available for reference but don't generate public pages. They are browsable on the venues and organisers pages if this group is confirmed as the editor for that content.
