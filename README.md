API Testing Projects — Postman & Newman

Postman collections for practicing API testing against public training/demo services. The collections can be run in Postman Desktop or from the command line with Newman.

    Run status: The HTML reports are snapshots from earlier runs. Re-run the collections and check the CLI summary before stating that a suite passes. A passing run has 0 failed assertions.

Collections and test websites
1. End2End — E-commerce API

    API base: https://rahulshettyacademy.com/api/ecom
    Web application: Rahul Shetty Academy Client
    Workflow: login, add a product with a multipart/form-data image, create an order, get order details, and perform cleanup where configured.
    The productImage form-data field needs a real local image file. The repository includes images.png as a small test fixture.

Important ID and cleanup note: The create-order response returns two different IDs:

    orders[0] is the order ID.
    productOrderId[0] is the product ID.

The request whose URL contains /product/delete-product/ deletes a product, not an order. Do not pass order_id to that product endpoint. A true order deletion requires a separate order-delete endpoint documented by the API; do not guess the route.

The request body uses {{productName}}. Set productName to a plain value in the local environment (for example, Football QA) so the API does not store the literal placeholder text.
2. EventAPI Testing

    API base: https://api.eventhub.rahulshettyacademy.com/api
    API documentation: EventHub API docs
    Workflow: registration/login and event create, read, and update requests.
    The collection uses the event_url variable for its base URL. Login scripts handle the authorization token; do not save or publish a live token.

Repository files

text

.
├── End2End.postman_collection.json
├── EventAPI Testing.postman_collection.json
├── images.png                         # small local image fixture for CreateProduct
├── End2End-report-UAT.html             # generated report snapshot, if included
├── EventAPI-report-UAT.html            # generated report snapshot, if included
├── README.md
└── local/                              # create locally; do not commit
    ├── UAT.postman_environment.json
    └── QA.postman_environment.json

The collection filename EventAPI Testing.postman_collection.json includes a space. Keep the quotes around it in commands.
Requirements

    Node.js LTS (npm is included) for Newman.
    Newman to run Postman collections from the command line.
    newman-reporter-htmlextra to generate readable HTML reports.
    Postman Desktop is optional for editing collections and running requests manually.
    Internet access and authorized test credentials for the demo APIs.

Install Newman and the HTML reporter

Install Node.js LTS first, then open Command Prompt in the repository folder and run:

cmd

npm install --global newman newman-reporter-htmlextra
node --version
npm --version
newman --version

If newman is not recognized after installation, close and reopen Command Prompt.
Prepare local environments and image file

    Create a local folder beside the collections.
    Export the needed Postman environment from Postman and save the real file locally, for example as local\UAT.postman_environment.json.
    Fill in the credentials and values required by the collection. Keep the current UAT/QA files local; only commit sanitized .example files with credentials and tokens removed.
    For EventAPI, make sure event_url is set to https://api.eventhub.rahulshettyacademy.com/api. The command below also shows how to supply it as a one-run override.
    For End2End, set productName to a real value and make sure the productImage file reference points to images.png on your machine. An absolute path such as C:/Users/... is machine-specific; update it after cloning this repository. Keep the image small (the demo API enforces an upload-size limit).
    Newman does not inherit the environment selected in Postman Desktop. Use -e to select the exported environment file.

Run the collections

Run these commands from the repository root in Windows Command Prompt. Replace the local environment path if you use QA instead of UAT.
End2End

cmd

newman run ".\End2End.postman_collection.json" -e ".\local\UAT.postman_environment.json" -r cli,htmlextra --reporter-htmlextra-export ".\End2End-report-UAT.html" --reporter-htmlextra-skipSensitiveData

EventAPI Testing

cmd

newman run ".\EventAPI Testing.postman_collection.json" -e ".\local\UAT.postman_environment.json" --env-var "event_url=https://api.eventhub.rahulshettyacademy.com/api" -r cli,htmlextra --reporter-htmlextra-export ".\EventAPI-report-UAT.html" --reporter-htmlextra-skipSensitiveData

--env-var overrides event_url for that run only. If the local environment file already contains the correct URL, you can remove that option.
Open the generated reports

cmd

start "" ".\End2End-report-UAT.html"
start "" ".\EventAPI-report-UAT.html"

The HTML reporter can still produce a report when tests fail. Check the Newman CLI summary and the report's failed-assertion count; do not treat an old report as proof of a current pass. --reporter-htmlextra-skipSensitiveData omits request/response headers and bodies, but inspect any report before sharing it.
Security and sharing

    Never commit raw QA/UAT environment files, passwords, JWT/access tokens, cookies, API keys, or private user data.
    Keep real environments in local/ and add that folder and raw environment filenames to
    .gitignore.
    If an environment example is needed, create a separate sanitized file such as UAT.example.postman_environment.json with secret values blanked or replaced by placeholders.
    Inspect collections and reports too; they can contain hard-coded credentials or sensitive request/response data.
    If a real credential or token has already been committed to a public repository, revoke/rotate it. Deleting the file in a later commit does not remove it from Git history.
    These are shared training/demo services. Use test data only, avoid high-volume traffic, and remember that create requests can leave products or orders in the demo application.


