# Document Validation

On this page

* [Behavior](https://docs.mongodb.com/manual/core/document-validation/#behavior)
* [Restrictions](https://docs.mongodb.com/manual/core/document-validation/#restrictions)
* [Bypass Document Validation](https://docs.mongodb.com/manual/core/document-validation/#bypass-document-validation)
* [Additional Information](https://docs.mongodb.com/manual/core/document-validation/#additional-information)

New in version 3.2.

MongoDB provides the capability to validate documents during updates and insertions. Validation rules are specified on a per-collection basis using the`validator`option, which takes a document that specifies the validation rules or expressions. Specify the expressions using any[query operators](https://docs.mongodb.com/manual/reference/operator/query/#query-selectors), with the exception of[`$near`](https://docs.mongodb.com/manual/reference/operator/query/near/#op._S_near),[`$nearSphere`](https://docs.mongodb.com/manual/reference/operator/query/nearSphere/#op._S_nearSphere),[`$text`](https://docs.mongodb.com/manual/reference/operator/query/text/#op._S_text), and[`$where`](https://docs.mongodb.com/manual/reference/operator/query/where/#op._S_where).

Add document validation to an existing collection using the[`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod)command with the`validator`option. You can also specify document validation rules when creating a new collection using[`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection)with the`validator`option, as in the following:

```
db.createCollection( "contacts",
   { validator: { $or:
      [
         { phone: { $type: "string" } },
         { email: { $regex: /@mongodb\.com$/ } },
         { status: { $in: [ "Unknown", "Incomplete" ] } }
      ]
   }
} )
```

MongoDB also provides the`validationLevel`option, which determines how strictly MongoDB applies validation rules to existing documents during an update, and the`validationAction`option, which determines whether MongoDB should`error`and reject documents that violate the validation rules or`warn`about the violations in the log but allow invalid documents.

## Behavior

Validation occurs during updates and inserts. When you add validation to a collection, existing documents do not undergo validation checks until modification.

### Existing Documents

You can control how MongoDB handles existing documents using the`validationLevel`option.

By default,`validationLevel`is`strict`and MongoDB applies validation rules to all inserts and updates. Setting`validationLevel`to`moderate`applies validation rules to inserts and to updates to existing documents that fulfill the validation criteria. With the`moderate`level, updates to existing documents that do not fulfill the validation criteria are not checked for validity.

EXAMPLE

Consider the following documents in a`contacts`collection:

```
{
   "_id": "125876",
   "name": "Anne",
   "phone": "+1 555 123 456",
   "city": "London",
   "status": "Complete"
},
{
   "_id": "860000",
   "name": "Ivan",
   "city": "Vancouver"
}
```

Issue the following command to add a validator to the`contacts`collection:

```
db.runCommand( {
   collMod: "contacts",
   validator: { $or: [ { phone: { $exists: true } }, { email: { $exists: true } } ] },
   validationLevel: "moderate"
} )
```

The`contacts`collection now has a validator with the`moderate`validationLevel. If you attempted to update the document with`_id`of`125876`, MongoDB would apply validation rules since the existing document matches the criteria. In contrast, MongoDB will not apply validation rules to updates to the document with`_id`of`860000`as it does not meet the validation rules.

To disable validation entirely, you can set`validationLevel`to`off`.

### Accept or Reject Invalid Documents

The`validationAction`option determines how MongoDB handles documents that violate the validation rules.

By default,`validationAction`is`error`and MongoDB rejects any insertion or update that violates the validation criteria. When`validationAction`is set to`warn`, MongoDB logs any violations but allows the insertion or update to proceed.

EXAMPLE

The following example creates a`contacts`collection with a validator that specifies that inserted or updated documents should match at least one of three following conditions:

* the
  `phone`
  field is a string
* the
  `email`
  field matches the regular expression
* the
  `status`
  field is either
  `Unknown`
  or
  `Incomplete`
  .

```
db.createCollection( "contacts",
   {
      validator: { $or:
         [
            { phone: { $type: "string" } },
            { email: { $regex: /@mongodb\.com$/ } },
            { status: { $in: [ "Unknown", "Incomplete" ] } }
         ]
      },
      validationAction: "warn"
   }
)
```

With the validator in place, the following insert operation fails the validation rules, but since the`validationAction`is`warn`, the write operation logs the failure and succeeds.

```
db.contacts.insert( { name: "Amanda", status: "Updated" } )
```

The log includes the full namespace of the collection and the document that failed the validation rules, as well as the time of the operation:

```
2015-10-15T11:20:44.260-0400 W STORAGE  [conn3] Document would fail validation collection: example.contacts doc: { _id: ObjectId('561fc44c067a5d85b96274e4'), name: "Amanda", status: "Updated" }
```

## Restrictions

You cannot specify a validator for collections in the`admin`,`local`, and`config`databases.

You cannot specify a validator for`system.*`collections.

## Bypass Document Validation

Users can bypass document validation using the`bypassDocumentValidation`option. For a list of commands that support the`bypassDocumentValidation`option, see[Document Validation](https://docs.mongodb.com/manual/release-notes/3.2/#rel-notes-document-validation).

For deployments that have enabled access control, to bypass document validation, the authenticated user must have[`bypassDocumentValidation`](https://docs.mongodb.com/manual/reference/privilege-actions/#bypassDocumentValidation)action. The built-in roles[`dbAdmin`](https://docs.mongodb.com/manual/reference/built-in-roles/#dbAdmin)and[`restore`](https://docs.mongodb.com/manual/reference/built-in-roles/#restore)provide this action.

## Additional Information

SEE ALSO

[`collMod`](https://docs.mongodb.com/manual/reference/command/collMod/#dbcmd.collMod),[`db.createCollection()`](https://docs.mongodb.com/manual/reference/method/db.createCollection/#db.createCollection),[`db.getCollectionInfos()`](https://docs.mongodb.com/manual/reference/method/db.getCollectionInfos/#db.getCollectionInfos).

  


