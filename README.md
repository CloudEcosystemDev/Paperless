# <p align="center" width="100%"> <img src="./logo.png" width="250" height="250"> </p> 
# <p align="center" width="100%"> Paperless Public API OIH Connector </p>

## Description

A generated OIH connector for the Paperless Public API API (version v1).

Generated from: https://api.paperless.io<br/>
Generated at: 2022-05-17T13:28:15+02:00

## API Description

Welcome to the public API of Paperless.<br/>

## Authorization

Supported authorization schemes:
- OAuth2 - For more information, see https://datatracker.ietf.org/doc/html/rfc6749

For OAuth 2.0 you need to specify OAuth Client credentials as environment variables in the connector repository:
* `OAUTH_CLIENT_ID` - your OAuth client id
* `OAUTH_CLIENT_SECRET` - your OAuth client secret

## Actions

### Create a new Document
> Creates a new Document. You can either create a new Document from scratch, provide a `document_id` of<br/>
> an existing document that then gets duplicated, or provide a `template_id` that should be used to prepare the<br/>
> new document from. When a document is duplicated/prepared you can also provide the same parameters as when you<br/>
> update a document to overwrite the values on the resulting document on creation. The `workspace_id` must be<br/>
> submitted in any case. When duplicating/preparing a document and the `workspace_id` differs from the workspace<br/>
> of the document or template, the new document gets duplicated "into the workspace" for the given `workspace_id`.<br/>
> Be aware that, to duplicate/prepare "into another workspace", the user needs to be able to manage documents<br/>
> on both, the source and the destination, workspaces for the operation to succeed.<br/>
> <br/>
> ## Advanced document creation<br/>
> <br/>
> The `createDocument` operation provides some advanced parameters, making it possible to create a document with<br/>
> all information it needs to be dispatched, when a document is created by duplicating an existing document<br/>
> or creating the new document from a template. Advanced document creation is not possible for documents<br/>
> that are created from scratch.<br/>
> <br/>
> ### Parameter `participants`<br/>
> <br/>
> The `participants` parameter is an object in which the keys are the `slot_name`s of the participants<br/>
> existing in the source document or template. The values of the `participant` object are objects with<br/>
> parameters for the corresponding participant in the newly created document.<br/>
> <br/>
> Example:<br/>
> <br/>
> Imagine a template with two participants with the `slot_names` `slot1` and `slot2`. The participants in the<br/>
> template do not have an email or a name yet. When supplying the `participants` parameter like this:<br/>
> <br/>
> ```<br/>
> {<br/>
>   "template_id": 1234,<br/>
>   ...<br/>
>   participants: {<br/>
>     "slot1": {"email": "p1@example.com", name: "P1"},<br/>
>     "slot2": {"email": "p2@example.com", name: "P2"}<br/>
>   }<br/>
> }<br/>
> ```<br/>
> <br/>
> The participants of the created document will have the correct values for the "email" and "name" attributes.<br/>
> <br/>
> ### Parameter `tokens`<br/>
> <br/>
> The `tokens` parameter is an object in which the keys are the `name`s of the tokens existing in the source<br/>
> document or template. The values of the `tokens` object are strings and represent the values that will<br/>
> be assigned to the tokens of the newly created document.<br/>
> <br/>
> ### Parameter `blocks`<br/>
> <br/>
> The `blocks` parameter is an object in which the keys are the `slug`s of the blocks existing in the source<br/>
> document or template. The values of the `blocks` object are objects which values will<br/>
> be assigned to the blocks of the newly created document. Not all block values are allowed.<br/>
> A list of allowed keys can be found in the parameter's schema.<br/>
> <br/>
> HINT: As the IDs of child blocks that would act as default values for checkbox, radio and select inputs are not<br/>
>       existent before the document is created, it is necessary to use the `default_value_from_value` attribute<br/>
>       to set default_values of those input blocks. The server will map the default_value of<br/>
>       the blocks by searching for the correct child blocks when they were created.<br/>
> <br/>
> ## Direct dispatch<br/>
> <br/>
> It is possible to directly dispatch a document after its creation without the need for an additional call<br/>
> to the `updateDocument` operation. A requirement to do so is, that all information required to dispatch<br/>
> the document were provided (participants, tokens etc.).<br/>
> <br/>
> To directly dispatch a document, after it was created, the client needs to supply the `state` with a value<br/>
> of `dispatched`.<br/>
> <br/>
> ## Initial participants dispatch strategy<br/>
> <br/>
> Normally the client creating a document will never be able to read tokens of any participants<br/>
> and the tokens are only distributed directly to the participants via email. Because this<br/>
> applies some restrictions for external clients integrating with Paperless, it is possible to set an<br/>
> `participants_dispatch_strategy` parameter when a document is created and directly dispatched.<br/>
> Providing this parameter will mark the participants which are at first turn<br/>
> as `state = dispatch_completed` and assigned them the `dispatch_strategy`<br/>
> `api_response_initial_participants`. As long as those<br/>
> participants are in a state which allows them to participate their token will be serialized in the response for<br/>
> the document. The client is then responsible to deliver the tokens to the participants as they will not get an<br/>
> email with their token from Paperless.<br/>
> <br/>
> NOTE: Only the participants at the first turn will be marked with the `dispatch_strategy`<br/>
> `api_response_initial_participants`. All participants further<br/>
> in the participation flow will get their token via email.<br/>

*Tags:* `Document`

### Update existing Document by its id
> Updates the Document for the given id and returns the updated resource on success.<br/>
> <br/>
> ## Dispatch the document<br/>
> <br/>
> It is possible to dispatch the document via updating its state to "dispatched".<br/>
> When doing so, the server runs several checks on the document, that is subject to be dispatched and<br/>
> responds with `200 success` if the dispatch was successful. If the server was not able to the dispatch the<br/>
> document, it responds with `422 unprocessable entity`. There are different reasons for the server, to not<br/>
> dispatch a document which are:<br/>
> <br/>
>   - Document has no participants<br/>
>   - Document has blocks with duplicated slugs (duplicated slugs are returned by within the validation error)<br/>
>   - When the client tries to dispatch a document, that was already dispatched<br/>
> <br/>
> ## Cancel the document<br/>
> <br/>
> A dispatched and not yet completed document can be cancelled by a user via updating its state to<br/>
> "cancelled". This will lead to all non completed participants getting disabled<br/>
> with the reason "cancelled". All participants that got an email<br/>
> about their participation will get an email about the cancellation.<br/>
> <br/>
> The server responds with `422 unprocessable entity` when the client tries to cancel a document, that was<br/>
> was not yet dispatched or already completed.<br/>

*Tags:* `Document`

#### Input Parameters
* `id` - _required_

### Get Document by its id
> Returns information about the Document for the given id.<br/>

*Tags:* `Document`

#### Input Parameters
* `id` - _required_

### Update an existing Webhook
> Updates the existing Webhook for the given id and returns the updated resource on success.<br/>

*Tags:* `Webhook`

#### Input Parameters
* `id` - _required_

### Delete an existing Webhook by its id
> Destroys the Webhook for the given id returns an empty object on success.<br/>

*Tags:* `Webhook`

#### Input Parameters
* `id` - _required_

### Get Submission by its id
> Returns information about the Submission for the given id.<br/>
> <br/>
> Submissions contain all data received from a recipient, including form field values, uploaded files, signatures<br/>
> and meta-data (timestamps, IP addresses etc).<br/>

*Tags:* `Submission`

#### Input Parameters
* `id` - _required_

### Create a new Webhook
> Creates a new Webhook.<br/>

*Tags:* `Webhook`

### Get Webhook by its id
> Returns information about the Webhook for the given id.<br/>

*Tags:* `Webhook`

#### Input Parameters
* `id` - _required_

### Delete an existing Document by its id
> Destroys the Document for the given id and returns an empty object on success.<br/>

*Tags:* `Document`

#### Input Parameters
* `id` - _required_

### Get Template by its id
> Returns information about the Template for the given id.<br/>

*Tags:* `Template`

#### Input Parameters
* `id` - _required_

## License

: paperless<br/>
                    <br/>

All files of this connector are licensed under the Apache 2.0 License. For details
see the file LICENSE on the toplevel directory.
