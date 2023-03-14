# Week 4 — Postgres and RDS

This week we are going to be using AWS Relational database (RDS) to store our user activity data.

### What is RDS ?

Amazon Relational Database Service (Amazon RDS) is a collection of managed services that makes it simple to set up, operate, and scale databases in the cloud.

This week we are going to be using postgres database under the RDS collection of database to store our user activity data.

### FIrst Step

- Create an RDS Datababe using the `AWS CLI`

we first have to test that our aws cli is properly created by issue the command below to get our aws identity.

```bash
aws sts get-caller-identity
```

