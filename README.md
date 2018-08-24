# SN{API} Library Template
Template configuration for SNAPI library

To create your custom library config, clone this repository and make changes to the configurations and content as required.

## Folder Structure
The folder structure is as follows:

- **libraries** (put your api library package in here)
  - `<package_folder>` (your package folder that contains your API libraries)
    - **config.json** (config file for your package)
    - **img** (images for your package)
    - `<api_library>` (folders for your API libraries)
    - ...
    - `<api_library>` (folders for your API libraries)
      - **config.json** (config file for your API library)
      - **img** (images for your API library)
      - **md** (markdown files for the content of your API library)
      - **specs** (API specs)


### Package Config
The package `config.json` looks like this:

```
{
  "//": "This is the config file for your API Package. Please follow the examples below.",
  "name": "ndi",
  "label": "NDI APIs",
  "desc": "COMING SOON - APIs for National Digital Identity (NDI)",
  "logo": "logo_ndi.png",
  "libraries": ["appwebdev","aspop","ds","ffprov","resowners","secra"]
}
```

**NOTE:**
* Make sure you name your package folder with the same name as the `name` in the `config.json`.
* Do not use dot `'.'` or underscore `'_'` in your package name.
* Make sure you list out the libraries in your package.


### API Library Config
The `config.json` file for each API library  looks like this:

```
{
  "//": "This is the config file for your API library. Please follow the examples below.",
  "libname": "appwebdev",
  "liblabel": "App & Web Developer",
  "libdesc": "Developers who are developing mobile or web app(also known as Relying Parties) will find this set of APIs useful",
  "defaultPath": "introduction",
  "logo": "logo_ndi.png",
  "items": [{
      "//": ["This is a menu item in your library",
        "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
        "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
      ],
      "path": "introduction",
      "text": "Introduction",
      "mdContent": "introduction.md"
    },
    {
      "//": ["This is a menu item in your library",
        "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
        "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
      ],
      "path": "implementation",
      "text": "Technical References",
      "mdContent": "implementation-appdev.md",
      "children": [
        {
          "//": ["This is a submenu item in your library",
            "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
            "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
          ],
          "path": "implementation-integration",
          "text": "Technical Reference for Mobile App Developers",
          "mdContent": "implementation-appdev.md"
        },
        {
          "//": ["This is a submenu item in your library",
            "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
            "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
          ],
          "path": "implementation-integration",
          "text": "Technical Reference for Web Developers",
          "mdContent": "implementation-webdev.md"
        },
        {
          "//": ["This is a submenu item in your library",
            "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
            "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
          ],
          "path": "implementation-integration",
          "text": "Integration",
          "mdContent": "implementation-integration.md"
        },
        {
          "//": ["This is a submenu item in your library",
            "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
            "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
          ],
          "path": "implementation-discoverydoc",
          "text": "Discovery Document",
          "mdContent": "implementation-discoverydoc.md"
        },
		    {
          "//": ["This is a submenu item in your library",
            "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
            "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
          ],
          "path": "implementation-security",
          "text": "Security",
          "mdContent": "implementation-security.md"
        },
		    {
          "//": ["This is a submenu item in your library",
            "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
            "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
          ],
          "path": "implementation-errorhandling",
          "text": "Error Handling",
          "mdContent": "implementation-errorhandling.md"
        }
      ]
    },
	  {
      "//": ["This is a menu item in your library",
        "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
        "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
      ],
      "path": "tutorial",
      "text": "Tutorials",
      "mdContent": "tutorial-gettingstarted.md",
			"children": [
				{
					"path": "tutorial-gettingstarted",
					"text": "Getting Started",
					"mdContent": "tutorial-gettingstarted.md"
				},
				{
					"path": "tutorial-integration",
					"text": "Integration",
					"mdContent": "tutorial-integration.md"
				},
				{
					"path": "tutorial-tryoutndilogin",
					"text": "Try out NDI Login",
					"mdContent": "tutorial-tryoutndilogin.md"
				}
			]
    },
	  {
      "//": ["This is a menu item in your library",
        "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
        "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
      ],
      "path": "resources",
      "text": "Resources",
      "mdContent": "resources.md"
    },
    {
      "//": ["This is a menu item in your library",
        "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
        "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
      ],
      "path": "support",
      "text": "Support",
      "mdContent": "support-contactus.md",
			"children": [
				{
					"path": "support-contactus",
					"text": "Contact Us",
					"mdContent": "support-contactus.md"
				},
				{
					"path": "support-faq",
					"text": "FAQs",
					"mdContent": "support-faq.md"
				}
			]
    },
    {
      "//": ["This is a menu item that redirects to an external link"],
      "path": "ndisandbox",
      "text": "NDI Sandbox",
      "redirectURL": "https://sandbox.api.ndi.gov.sg"
    },
    {
      "//": ["This is horizontal divider for your menu"],
      "hr": "--"
    },
    {
      "path": "apispecs",
      "text": "API Specifications",
      "children": [
        {
          "//": ["This is a submenu item in your library",
            "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
            "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
          ],
          "path": "apispecs-aspv001",
          "text": "ASP v0.0.1",
          "apiSpecs": "aspv0.0.1.yaml"
        },
        {
          "//": ["This is a submenu item in your library",
            "Provide your content using md (MarkDown) files the `md folder`. `mdContent` specifies the name of the md file.",
            "Refer to https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet for markdown syntax."
          ],
          "path": "apispecs-authzv001",
          "text": "authz v0.0.1",
          "apiSpecs": "authzv0.0.1.yaml"
        }
      ]
    }
  ]
}
```

**NOTE:**
*  Make sure you name your API library folders with the same name as the `libname` in the `config.json`.
* Do not use dot `'.'` or underscore `'_'` in your API library `libname` and `path` attributes.
