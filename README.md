# Group-Managed Content Setup Guide

This guide explains how to set up multiple content repositories where each group manages their own content (events, venues, and organisers) whilst sharing common taxonomies.

## Overview

The site structure allows:
- Each group to manage their own events, venues, and organisers
- Automatic JSON endpoints for all content types
- Clear separation of content ownership
- Flexible content organization per group

## 1. Main Site Setup

### Initialize as Hugo Module

```bash
# In the main site repository
hugo mod init github.com/ouroakley/main
```

### Configure Module Mounts

Add to your `hugo.yaml`:

```yaml
module:
  mounts:
    - source: content
      target: content
  imports:
    - path: github.com/ouroakley/example-group
      mounts:
        - source: events
          target: content/events/example-group
        - source: venues
          target: content/venues
        - source: organisers
          target: content/organisers

# Enable JSON output for all content types
outputs:
  home:
    - HTML
  section:
    - HTML
    - JSON
  taxonomy:
    - HTML
    - JSON
  term:
    - HTML
    - JSON
```

## 2. Group Repository Setup

### Create New Group Repo

1. Create a new repository (e.g., `example-group``)
2. Initialize as Hugo module:
```bash
hugo mod init github.com/ouroakley/example-group
```

### Group Repository Structure
```
├── content/
│   ├── events/
│   │   └── event-one/
│   │       └── index.md
│   ├── venues/
│   │   └── venue-one/
│   │       └── _index.md
│   └── organisers/
│       └── organiser-one/
│           └── _index.md
├── static/
│   └── admin/
│       ├── config.yml
│       └── index.html
└── go.mod
```

### Group Decap CMS Configuration

Create `static/admin/config.yml` in the group repo:

```yaml
backend:
  name: github
  repo: ouroakley/group-one  # Change for each group
  branch: main
  site_domain: https://www.ouroakley.uk
  base_url: https://www.ouroakley.uk
  auth_endpoint: auth

publish_mode: editorial_workflow
media_folder: "static/images/uploads"
public_folder: "/images/uploads"

collections:
  - name: "events"
    label: "Events"
    label_singular: "Event"
    folder: "content/events"
    create: true
    nested:
      depth: 100
      summary: '{{title}}'
    fields:
      - {label: "Title", name: "title", widget: "string"}
      - {label: "Description", name: "description", widget: "text", required: false}
      - {label: "Event Start", name: "eventStart", widget: "datetime"}
      - {label: "Event End", name: "eventEnd", widget: "datetime", required: false}
      - label: "Venues"
        name: "venues"
        widget: "relation"
        collection: "venues"
        search_fields: ["title", "address"]
        value_field: "{{slug}}"
        display_fields: ["title"]
        multiple: true
        options_url: "https://www.ouroakley.uk/venues/index.json"
      - label: "Organisers"
        name: "organisers"
        widget: "relation"
        collection: "organisers"
        search_fields: ["title", "fullName"]
        value_field: "{{slug}}"
        display_fields: ["title"]
        multiple: true
        options_url: "https://www.ouroakley.uk/organisers/index.json"
      - {label: "Content", name: "body", widget: "markdown"}

  - name: "venues"
    label: "Venues"
    label_singular: "Venue"
    folder: "content/venues"
    create: true
    nested:
      depth: 100
      summary: '{{title}}'
    meta:
      path:
        widget: string
        label: 'Path'
        index_file: '_index'
    fields:
      - {label: "Title", name: "title", widget: "string"}
      - {label: "Description", name: "description", widget: "text", required: false}
      - {label: "Address", name: "address", widget: "string"}
      - {label: "Weight", name: "weight", widget: "number", required: false, hint: "Optional: Controls sorting order"}
      - {label: "Content", name: "body", widget: "markdown", required: false}

  - name: "organisers"
    label: "Organisers"
    label_singular: "Organiser"
    folder: "content/organisers"
    create: true
    nested:
      depth: 100
      summary: '{{title}}'
    meta:
      path:
        widget: string
        label: 'Path'
        index_file: '_index'
    fields:
      - {label: "Title", name: "title", widget: "string"}
      - {label: "Description", name: "description", widget: "text", required: false}
      - {label: "Full Name", name: "fullName", widget: "string"}
      - {label: "Weight", name: "weight", widget: "number", required: false, hint: "Optional: Controls sorting order"}
      - {label: "Content", name: "body", widget: "markdown", required: false}
```

## 3. JSON Endpoints

Hugo automatically generates JSON endpoints for all content types:
- `/events/index.json` - List of all events from all groups
- `/venues/index.json` - List of all venues from all groups
- `/organisers/index.json` - List of all organisers from all groups

Example JSON outputs:
```json
{
    "events": [
        {
            "value": "group-one/event-one",
            "label": "Event One",
            "eventStart": "2024-03-20T10:00:00Z"
        }
    ],
    "venues": [
        {
            "value": "group-one/venue-one",
            "label": "Venue One",
            "address": "123 Main St"
        }
    ],
    "organisers": [
        {
            "value": "group-one/organiser-one",
            "label": "Organiser One",
            "fullName": "John Doe"
        }
    ]
}
```

## 4. Content Management Guidelines

### Folder Structure
Each group's events content should be mounted in their own namespace:
```
content/
├── events/
│   ├── group-one/
│   │   ├── event-one/
│   │   └── event-two/
│   └── group-two/
│       ├── event-one/
│       └── event-two/
├── venues/
│   ├── venue-one/
│   │   └── _index.md
│   └── venue-two/
│       └── _index.md
└── organisers/
    ├── organiser-one
    │   └── _index.md
    └── organiser-two
        └── _index.md
```

### Content Sorting
- Use the `weight` parameter in front matter to control sorting
- Lower numbers appear first in lists
- Example:
  ```yaml
  ---
  title: "Event One"
  weight: 1  # Will appear first in lists
  ---
  ```

## 5. Benefits

1. Group Autonomy:
   - Groups can create and manage their own content
   - Full control over content details and organization
   - No need for central approval of changes

2. Namespace Separation:
   - Clear ownership of events content
   - Prevents naming conflicts between groups
   - Easy to identify content source

3. Unified Experience:
   - All content available in JSON endpoints
   - Consistent content selection in CMS
   - Shared content structure

4. Maintainability:
   - Decentralized content management
   - Reduced central administration
   - Groups can update their content independently

