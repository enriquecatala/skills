<div class="social_links">    
    <a href="https://github.com/enriquecatala" target="_blank"><img  src="https://img.shields.io/github/followers/enriquecatala?label=GitHub&style=for-the-badge" alt="GitHub followers of Enrique Catalá" ></a>
    <a href="https://github.com/sponsors/enriquecatala" target="_blank"><img src="https://img.shields.io/badge/GitHub_Sponsors--_.svg?style=for-the-badge&logo=github&logoColor=EA4AAA" alt="Sponsor Enrique Catalá on GitHub" ></a>    
    <a href="https://www.linkedin.com/in/enriquecatala" target="_blank"><img src="https://img.shields.io/badge/LinkedIn--_.svg?style=for-the-badge&logo=linkedin" alt="LinkedIn Enrique Catalá" ></a>        
    <a href="https://twitter.com/enriquecatala" target="_blank"><img src="https://img.shields.io/twitter/follow/enriquecatala?color=blue&label=twitter&style=for-the-badge" alt="X @enriquecatala" ></a>    
    <a href="https://enriquecatala.com"><img src="https://img.shields.io/website?down_color=red&down_message=down&label=enriquecatala.com&up_color=46C018&url=https%3A%2F%2Fenriquecatala.com&style=for-the-badge" alt="Data Engineering with Enrique Catalá"></a>    
</div> 

<div style="display: flex; align-items: center; justify-content: center;">
  <a href="https://www.credly.com/badges/cde0dbd2-8d03-4ca7-8284-d471d65d0e5f">
      <img src="https://raw.githubusercontent.com/enriquecatala/enriquecatala/master/img/MVP_Logo_horizontal.png" 
           alt="Microsoft DataPlatform and AI MVP Enrique Catalá"
           style="min-height: 50px; max-height: 70px; min-width: 100px">
  </a>
</div>

- [Agent Skills Collection](#agent-skills-collection)
  - [Installation](#installation)
  - [Available Skills](#available-skills)
    - [Draw.io Generator (`drawio-generator`)](#drawio-generator-drawio-generator)


# Agent Skills Collection

This repository contains my own personal collection of AI Agent Skills. These skills extend the capabilities of AI coding agents by providing specialized instructions and tools based on my own experience and preferences.

## Installation

You can install skills from this repository using `npx`:

```bash
npx skills add https://github.com/enriquecatala/skills
```

To install a specific skill, use the `--skill` flag:

```bash
npx skills add https://github.com/enriquecatala/skills --skill <skill-name>
```

## Available Skills

### Draw.io Generator (`drawio-generator`)

A powerful skill for generating professional Draw.io (mxGraph) architecture diagrams from text descriptions.

**Features:**
- **Text-to-Diagram:** Generates complete diagrams from natural language prompts.
- **Vendor Stencils:** Includes support for 8900+ vendor stencils (AWS, Azure, GCP, Cisco, Kubernetes, etc.).
- **Validation:** Includes a built-in validation script to ensure generated XML is correct and renderable.
- **Iterative Workflow:** Supports an iterative design process with automatic error correction.

**Installation:**

```bash
npx skills add https://github.com/enriquecatala/skills --skill drawio-generator
```

**Usage:**
Once installed, you can ask your agent to "create a draw.io diagram of a 3-tier AWS architecture" or "visualize this text description as a flow chart". The agent will use the skill's instructions to generate the file.

