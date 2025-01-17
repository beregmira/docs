---
sourcePath: en/tracker/api-ref/concepts/issues/add-comment.md
---
# Adding a comment

Use this request to add a comment to an issue.

## Request format {#section_rkq_bhl_dfb}

Before making the request, [get permission to access the API](../access.md).

To add a comment to an issue, use an HTTP `POST` request. Provide the request parameter in the request body in JSON format:

```json
POST /{{ ver }}/issues/<issue-id>/comments
Host: {{ host }}
Authorization: OAuth <OAuth token>
{{ org-id }}

{
   "text": "<comment text>"
}
```

{% include [headings](../../../_includes/tracker/api/headings.md) %}

{% include [resource-issue-id](../../../_includes/tracker/api/resource-issue-id.md) %}

{% cut "Request parameters" %}

**Additional parameters**

| Parameter | Description | Data type |
| ----- | ----- | ----- |
| isAddToFollowers | Add the user who made the comment to followers (by default, `true`). | Logical |

{% endcut %}

{% cut "Request body parameters" %}

**Required parameters**

| Parameter | Value | Data type |
| ----- | ----- | ----- |
| text | Comment on the issue. | String |

**Additional parameters**

| Parameter | Value | Data type |
| ----- | ----- | ----- |
| attachmentIds | List of [attachment IDs](temp-attachment.md). | Array of strings |
| summonees | IDs or usernames of invited users. | Array of objects or strings |
| maillistSummonees | List of mailing lists mentioned in the comment. | Array of strings |

{% endcut %}

## Response format {#section_rcd_ysf_2fb}

{% list tabs %}

- Request executed successfully

    {% include [answer-201](../../../_includes/tracker/api/answer-201.md) %}

    The response body contains information about the added comment in JSON format.

    ```json
    [
    {
      "self" : "https://{{ host }}/v2/issues/TREK-1/comments/626",
      "id" : 626,
      "longId" : "5fa15a24ac894475dd14ff07",
      "text" : "<comment text>",
      "createBody" : {
       "self" : "https://{{ host }}/v2/users/1120000000016876",
       "id" : "<user ID>",
       "display" : "<displayed user name>"
      },
      "updateBody" : {
       "self" : "https://{{ host }}/v2/users/1120000000016876",
       "id" : "<user ID>",
       "display" : "<displayed user name>"
      },
      "createdAt" : "2020-11-03T13:24:52.575+0000",
      "updatedAt" : "2020-11-03T13:24:52.575+0000",
      "summonees" : [
       {
        "self" : "https://{{ host }}/v2/users/1120000000016576",
        "id" : "<user ID>",
        "display" : "<displayed user name>"
       }
      ],
     "maillistSummonees" : [
       {
        "self" : "https://{{ host }}/v2/maillists/usertest@test.ru",
        "id" : "<mailing list address>",
        "display" : "<displayed mailing list name>"
       }
      ],
      "version" : 1,
      "type" : "standard",
      "transport" : "internal"
    }
    ]
    ```

    {% cut "Response parameters" %}

    | Parameter | Description | Data type |
    | ----- | ----- | ----- |
    | self | Link to the comment. | String |
    | id | Comment ID. | Number |
    | longId | ID of the comment in string format. | String |
    | text | Comment on the issue. | String |
    | [createdBy](#object-fields-createdBy) | Block with information about the user who added the comment. | Object |
    | [updatedBy](#object-fields-updatedBy) | Block with information about the user who edited the comment last. | Object |
    | createdAt | Comment creation date and time. | String |
    | updatedAt | Comment update date and time. | String |
    | [summonees](#object-fields-summonees) | Block with information about users who are invited in comments. | Object |
    | [maillistsummonees](#object-fields-maillistsummonees) | Block with information about mailing lists mentioned in comments. | Object |
    | version | Comment version. Each update increases the comment's version number. | Number |
    | type | Comment type:<ul><li>`standard`: Comment sent via the {{ tracker-name }} interface.</li><li>`incoming`: Comment created from an incoming message.</li><li>`outcoming`: Comment created from an outgoing message.</li></ul> | String |
    | transport | Method of adding a comment:<ul><li>`internal`: Via the {{ tracker-name }} interface.</li><li>`email`: Via email.</li></ul> | String |

    `createdBy` **object fields** {#object-fields-createdBy}

    {% include [user](../../../_includes/tracker/api/user.md) %}

    `updatedBy` **object fields** {#object-fields-updatedBy}

    {% include [user](../../../_includes/tracker/api/user.md) %}

    `summonees` **object fields** {#object-fields-summonees}

    {% include [user](../../../_includes/tracker/api/user.md) %}

    `maillistsummonees` **object fields** {#object-fields-maillistsummonees}

    | Parameter | Description | Data type |
    | ----- | ----- | ----- |
    | self | Link to the mailing list. | String |
    | id | Mailing list address. | String |
    | display | Mailing list name displayed. | String |

    {% endcut %}

- Request failed

    If the request is processed incorrectly, the API returns a response with an error code:

    {% include [answer-error-404](../../../_includes/tracker/api/answer-error-404.md) %}

{% endlist %}

