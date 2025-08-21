---
subtitle: check
---

## Regular expression rules format

When using regular expression rules for static code analysis through [check -code](<Commands/Check/Check Code>), the format of the [TOML](https://toml.io/en/) rules files is as follows:

| Field            | Purpose                                                  | Type               | Possible values                                                                                               | Example                                               |
| ---------------- | -------------------------------------------------------- | ------------------ | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
| dialects         | Which dialect of SQL does this rule apply to             | Array (of Strings) | `text`, `bigquery`, `db2`, <BR>`mysql`, `oracle`, `postgres`,<BR>`redshift`, `snowflake`,<BR>`sqlite`, `tsql` | ["text"]                                              |
| rules            | The regex rule you want                                  | Array (of Strings) | [Regular Expressions](https://www.regular-expressions.info/)                                                  | ["your regex here"]                                   |
| description      | Allows a more in-depth description of the rule           | String             | Anything                                                                                                      | "Descriptive comment that will appear in your report" |
| severity         | Controls how violations are handled                      | String             | `error`, `warning`, `disabled`                                                                                | "error"                                               |

### Dialects

The way your regex rule is structured will vary depending on the dialect of SQL in use with your database (different keywords and syntax) so you may need explicitly declare the dialect that this rule is relevant for.

Flyway will identify the variety of SQL relevant to database based on the JDBC connection string and only apply relevant rules (so a rule declared for the Oracle dialect won't be applied when using a PostgreSQL database).

* The `TEXT` dialect means the rule will be applied to all migrations regardless of the DB type Flyway is configured to use.

### Severity

The `severity` field controls how violations of this rule are handled:

* `error` - Violations will cause the check command to fail when [`check.code.strictMode`](<Configuration/Flyway Namespace/Flyway Check Namespace/Flyway Check Code Strict Mode Setting>) is enabled
* `warning` - Violations will be reported but will not cause the operation to fail
* `disabled` - The rule will be ignored and no violations will be reported

The `disabled` severity level allows you to temporarily disable specific rules without removing them from your configuration.

### passOnRegexMatch [Deprecated]

| Value | Purpose                                                                                                                                                 |
| ----- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| false | There is something in my migration that the regex matches - I want this rule to flag a violation in this case                                           |
| true  | I want a particular style or pattern in my code (for example, something standard in every migration script). If it is *not* there then flag a violation |

The parameter is deprecated in Regex Code Analysis and will be ignored in future releases. Regex matches will always be treated as violations. 
If your previous configuration relied on `passOnRegexMatch = true` (i.e. expecting the pattern to be present), you will need to invert your Regex to match cases where the pattern is absent instead.

### Regular expression considerations

* Does case sensitivity matter to you ? If it doesn't then make the regex rules insensitive too with the prefix `(?i)`
* You will need to use the [Java dialect](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html) of regex

### File content example

```
dialects = ["TEXT"]
rules = ["(?i)(^|\\s)TO\\s+DO($|\\s|;)"]
passOnRegexMatch = false
description = "Phrase 'to do' remains in the code"
severity = "error"
```

## Built in enterprise rules

Redgate has added a set of rules that are of interest to customers with a larger and more complex database infrastructure. 

## RX001
_DROP TABLE statement_

**Dialects:** All

Dropping a table is likely to result in the loss of data so should be investigated before continuing.
## RX002
_Attempt to change password_

**Dialects:** Oracle/PostgreSQL/TSQL

Changing passwords through a DB migration is not considered best practice
## RX003
_TRUNCATE statement used_

**Dialects:** All

This operation is likely to result in a loss of data so should be investigated before continuing

## RX004
_DROP COLUMN statement used_

**Dialects:** All

This operation is likely to result in a loss of data so should be investigated before continuing
## RX005
_GRANT TO PUBLIC statement used_

**Dialects:** All

It is not common to access to this degree so should be investigated before continuing 

## RX006
_GRANT WITH GRANT OPTION statement used_

**Dialects:** All

Allows grantee to grant additional permissions and so it becomes difficult to track the scope of permissions 

## RX007
_GRANT WITH ADMIN OPTION statement used_

**Dialects:** All

Allows grantee to grant administrative permissions and so it becomes difficult to control the scope of permissions 

## RX008
_ALTER USER statement used_

**Dialects:** All

Modifies the properties of an existing user and should be investigated before continuing

## RX009
_GRANT ALL statement used_

**Dialects:** All

It is not common to access to this degree so should be investigated before continuing
## RX010
_CREATE ROLE statement used_

**Dialects:** All
## RX011
_ALTER ROLE statement used_

This is used to modify user accounts so should be investigated before continuing

**Dialects:** All
## RX012
_DROP PARTITION statement used_

**Dialects:** All
## RX013
_CREATE TABLE statement without a PRIMARY KEY constraint_

**Dialects:** All
## RX014
_A table has been created but has no `MS_Description` property added_

**Dialects:** TSQL

It is a good practice to include a description in the `MS_Description` extended property to document the purpose of a table.