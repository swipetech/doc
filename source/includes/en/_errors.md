# Error handling

An [error](#error) has fields that help finding the cause of the problem.

## code

The `code` field shows the error group. Most groups have a `sub_errors` field with more specific details. The groups are the following:

- `internal_server_error`: Internal application error.

- `bad_request`: Badly built request. This usually happens when the `body` is not in the expected format.

- `not_found`: Resource not found.

- `unauthorized`: Unauthorized. Represents an API authentication problem.

- `validation_error`: Validation error. This means that a specified value is invalid. In this case, each sub-error has a `field` which details the invalid field.

- `transfer_error`: Failed Transfer.

- `insecure_connection`: Insecure connection. Remember that `https` must be used in all requests.

## msg

This field has a human-readable message about the cause of the problem.

## sub_errors

[`sub_errors`](#suberror) has a list of sub-errors with specific details on the cause of the problem.
Similarly to [errors](#error), it carries the fields `code`, `msg` (and `field` in case of a validation error):

### `not_found`

- `org_not_found`: Organização not found.



### `validation_error`

- `pagination_limit`: The `limit` field for pagination must be a positive number

- `pagination_starting_after`: The `starting_after` field for pagination must be a positive number

- `invalid_token`: Invalid token

- `org_name_already_exists`: There already is an Organization with this name

- `org_name_empty`: The name of the Organization cannot be empty

- `org_network_required`: The `Network` field is required

- `ast_code_is_empty`: The Asset code cannot be empty

- `ast_code_invalid`: The Asset code is invalid; only letters and numbers are allowed

- `ast_code_already_exists`: There already is an Asset with this code

- `ast_code_invalid_length`: Invalid character amount for the Asset code; max of 12 characters

- `ast_invalid_limit`: Invalid Asset limit

- `ast_not_supported`: Asset not supported

- `transfer_from_is_empty`: The sender of the Transfer cannot be empty.

- `transfer_to_is_empty`: The receiver of the Transfer cannot be empty.

- `transfer_asset_id_is_empty`: Asset being sent in the Transfer cannot be empty.

- `transfer_invalid_amount`: The amount to be transferred is invalid.

- `transfer_invalid_actions_length`: Invalid number of Transfers in the batch.

- `act_invalid_actions_length`: Invalid number of Actions in the batch.

- `tag_invalid_value`: Invalid value. Tags must be only alphanumeric characters and/or `-_\/|:`, and have a length between 2 and 200

- `tag_invalid_size`: Tag limit exceeded. The max is of 10 tags per entity

### `transfer_error`

- `transfer_asset_not_found`: Asset not found

- `transfer_from_not_found`: Sender not found

- `transfer_to_not_found`: Receiver not found

- `transfer_from_asset_not_supported`: Asset not supported by the sender Account

- `transfer_to_asset_not_supported`: Asset not supported by the receiver Account

- `transfer_underfunded`: Insufficient balance for the Transfer

### `unauthorized`

- `acc_should_not_have_balances`: Account cannot be destroyed unless all of its balances are zero
