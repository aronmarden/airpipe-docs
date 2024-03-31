---
sidebar_position: 2
slug: /transforms/functions
displayed_sidebar: configurationSidebar
---

# Transforms

Are an **array of functions** which are used to transform your data after it is received. To use **transform functions** you must declare a `post_transforms` block in your specified **action**

<!-- link an action here -->
<!-- check example for renameKeys-->

```yml
actions:
  - name: Example
    input: a|body
    post_transforms:
      - extract_value: '[0]'
      - rename_keys:
          usernameID: userId
```

## Add Attribute

To add an attribute to your action you can use the `add_attribute` function. This will add an extra attribute to your data. You can use data from your input or add your own information for example an enviornment attribute.

**Add Attribute Example**

```yml
post_transforms:
  - add_attribute:
      env: 'Production'
      pageId: a|body::id|
```

This will result in -> Example

## Add JWT

To add a JWT token to your action you require the below fields.

- key: type of key jwt
- secret: the JWT Secret
- exp: the JWTs expiry
- data

**Add JWT Example**

```yml
- add_jwt:
    key: jwt
    # need to pull from secrets
    secret: a|var::JWT_SECRET|
    exp: 1d
    data: [email]
```

This will add a JWT to a response. For example attaching a JWT to a succesful user login.

## Bcrypt

To easily hash and verify passwords Air Pipe implements the Bcrypt Crate. Requires the below fields.

- key: password_hash
- value: `password`
- cost: ?

**Bcrypt Example**

```yml
- bcrypt:
    key: password_hash
    value: password
    cost: 12
```

## Extract Value

This function is used to extract values from your input. Usage `extract_value`

**Extract Value Example**

```yml
- name: CheckHeaderToken
  input: a|headers
  assert:
    tests:
      - value: airpipe-token
        is_valid_jwt: a|env::JWT_SECRET|
        is_not_null: true
  post_transforms:
    - extract_value: jwt_claims.data
```

The above action checks the header for `jwt_claims` and reads it with `.data`.

## Group By

This function will group your data by a particular field.

**Group By Example**
Consider an API that returns animnals.

```json
[
    {
        "name": "leo",
        "animal": "dog",
        "colour: "black"
    },
    {
        "name": "bella",
        "animal": "dog",
        "colour: "mixed"
    },
    {
        "name": "bob",
        "animal": "cat",
        "colour: "orange"
    }
]
```

If you want to order this by the animal field you can add the below.

```yml
post_transforms:
  - group_by: [a|animal::group_by<OR>string()|]
```

This would then output the data as:

```json
{
    "dog" : [
            {
        "name": "leo",
        "animal": "dog",
        "colour: "black"
        },
        {
            "name": "bella",
            "animal": "dog",
            "colour: "mixed"
        }
    ],
    "cat": [
         {
            "name": "bob",
            "animal": "cat",
            "colour: "orange"
        }
    ]
}
```

## Generate Password

**Generate Password Example**

```yml
post_transforms:
  - generate_password:
      key: password
      length: 10
```

## Numerics

## Rename Keys

This function is used to rename keys for better readability, usage and consistency.

**Rename Keys Example:** Consider an API that returns animnals.

```json
[
    {
        "name": "leo",
        "animal": "dog",
        "colour: "black"
    },
    {
        "name": "bella",
        "animal": "dog",
        "colour: "mixed"
    },
    {
        "name": "bob",
        "animal": "cat",
        "colour: "orange"
    }
]
```

We can rename the `colour` field to `col`.

```yml
actions:
  - name: Example
    input: a|body
    post_transforms:
      - rename_keys:
          colour: col
```

This would output the data as:

```json
[
    {
        "name": "leo",
        "animal": "dog",
        "col: "black"
    },
    {
        "name": "bella",
        "animal": "dog",
        "col: "mixed"
    },
    {
        "name": "bob",
        "animal": "cat",
        "col: "orange"
    }
]
```

## Remove Keys

This function is used to rename keys for better readability, usage and consistency.

**Rename Keys Example:**
Consider an API that returns animnals.

```json
[
    {
        "name": "leo",
        "animal": "dog",
        "colour: "black"
    },
    {
        "name": "bella",
        "animal": "dog",
        "colour: "mixed"
    },
    {
        "name": "bob",
        "animal": "cat",
        "colour: "orange"
    }
]
```

We can remove the `colour` field.

```yml
actions:
  - name: Example
    input: a|body
    post_transforms:
      - remove_keys:
          colour: col
```

This would output the data as:

```json
[
  {
    "name": "leo",
    "animal": "dog"
  },
  {
    "name": "bella",
    "animal": "dog"
  },
  {
    "name": "bob",
    "animal": "cat"
  }
]
```