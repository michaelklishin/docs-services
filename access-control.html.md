---
title: Access Control
---

_This page assumes that you are using cf v6_.

By default, all new service plans are private. This means that when adding a new broker, or when adding a new plan to an existing broker's catalog and updating the broker in Cloud Foundry, the plans won't immediately be available to end users. This enables an admin to control which service plans are available to end users, and to manage limited availability. This is important as in many cases Cloud Foundry admins will not have operational control of service brokers as they may be deployed and managed by other service providers.
  
Access control requires using `cf curl`.

Note: `cf curl` can only be run by an **account** administrator.

## <a id='make-plans-public'></a>Making Service Plans Public ##

To make a service plan public, you need the service plan GUID.
To find the service plan GUID, run:

`cf curl /v2/service_plans -X 'GET'`

This command returns a filtered JSON response listing every service plan.
Data about each plan shows in two sections: `metadata` and `entity.`
The `metadata` section shows the service plan GUID, while the `entity` section
lists the name of the plan.

Note: Because `metadata` is listed before `entity` for each service plan, the
GUID of a plan is shown six lines above the name.

Example:

<pre class="terminal">
$ cf curl /v2/service_plans
...
{
      "metadata": {
        "guid": "1afd5050-664e-4be2-9389-6bf0c967c0c6",
        "url": "/v2/service_plans/1afd5050-664e-4be2-9389-6bf0c967c0c6",
        "created_at": "2014-02-12T06:24:04+00:00",
        "updated_at": "2014-02-12T18:46:52+00:00"
      },
      "entity": {
        "name": "plan-name-1",
        "free": true,
        "description": "plan-desc-1",
        "service_guid": "d9011411-1463-477c-b223-82e04996b91f",
        "extra": "{\"bullets\":[\"bullet1\",\"bullet2\"]}",
        "unique_id": "plan-id-1",
        "public": false,
        "service_url": "/v2/services/d9011411-1463-477c-b223-82e04996b91f",
        "service_instances_url": "/v2/service_plans/1afd5050-664e-4be2-9389-6bf0c967c0c6/service_instances"
      }
}
</pre>

In this example, the GUID of plan-name-1 is 1afd5050-664e-4be2-9389-6bf0c967c0c6.

To make a service plan public, run:

`cf curl /v2/service_plans/SERVICE_PLAN_GUID -X 'PUT' -d '{"public":true}'`

As verification, the "entity" section of the JSON response shows the `"public":
true` key-value pair.

<pre class="terminal">
$ cf curl /v2/service_plans/1113aa0-124e-4af2-1526-6bfacf61b111 -X 'PUT' -d '{"public":true}'

    {
      "metadata": {
        "guid": "1113aa0-124e-4af2-1526-6bfacf61b111",
        "url": "/v2/service_plans/1113aa0-124e-4af2-1526-6bfacf61b111",
        "created_at": "2014-02-12T06:24:04+00:00",
        "updated_at": "2014-02-12T20:55:10+00:00"
      },
      "entity": {
        "name": "plan-name-1",
        "free": true,
        "description": "plan-desc-1",
        "service_guid": "d9011411-1463-477c-b223-82e04996b91f",
        "extra": "{\"bullets\":[\"bullet1\",\"bullet2\"]}",
        "unique_id": "plan-id-1",
        "public": true,
        "service_url": "/v2/services/d9011411-1463-477c-b223-82e04996b91f",
        "service_instances_url": "/v2/service_plans/1113aa0-124e-4af2-1526-6bfacf61b111/service_instances"
      }
    }
</pre>

Once made public, the service plan can be seen by all users in the list of
available services.
See [Managing Services](../devguide/services/managing-services.html) for
more information.

### <a id='make-plans-private'></a>Making Service Plans Private ###

To make a service plan private, follow the instructions in [Making Service Plans Public](#make-plans-public), but replace `"public":true` with `"public":false`.

Example making plan-name-1 private:

<pre class="terminal">
$ cf curl /v2/service_plans/1113aa0-124e-4af2-1526-6bfacf61b111 -X 'PUT' -d '{"public":false}'

    {
      "metadata": {
        "guid": "1113aa0-124e-4af2-1526-6bfacf61b111",
        "url": "/v2/service_plans/1113aa0-124e-4af2-1526-6bfacf61b111",
        "created_at": "2014-02-12T06:24:04+00:00",
        "updated_at": "2014-02-12T20:55:10+00:00"
      },
      "entity": {
        "name": "plan-name-1",
        "free": true,
        "description": "plan-desc-1",
        "service_guid": "d9011411-1463-477c-b223-82e04996b91f",
        "extra": "{\"bullets\":[\"bullet1\",\"bullet2\"]}",
        "unique_id": "plan-id-1",
        "public": false,
        "service_url": "/v2/services/d9011411-1463-477c-b223-82e04996b91f",
        "service_instances_url": "/v2/service_plans/1113aa0-124e-4af2-1526-6bfacf61b111/service_instances"
      }
    }
</pre>

## <a id='limited-availability'></a>Limited Availability ##

To make a private plan available to a specific organization, you need the GUID
of both the organization and of the service plan.

Users have access to a private plan only when targeting an organization that the plan has been enabled for. See [Managing Services](../devguide/services/managing-services.html) for more information.

You can find the GUID of the service plan using the command described in [Making Service Plans Public](#make-plans-public):

`cf curl -X 'GET' /v2/service_plans`

To find the organization GUIDs, run:

`cf curl /v2/organizations -X 'GET'`

This command returns a filtered JSON response listing every organization in your account.  Data about each organization shows in two sections: `metadata` and `entity`. The `metadata` section shows the organization GUID, while the `entity` section lists the name of the organization.

Note: Because `metadata` is listed before `entity` for each organization, the
GUID of an organization is shown six lines above the name.

Example:

<pre class="terminal">
$ cf curl /v2/organizations
{
  "metadata": {
    "guid": "c54bf317-d791-4d12-89f0-b56d0936cfdc",
    "url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc",
    "created_at": "2013-05-06T16:34:56+00:00",
    "updated_at": "2013-09-25T18:44:35+00:00"
  },
  "entity": {
    "name": "my-org",
    "billing_enabled": true,
    "quota_definition_guid": "52c5413c-869f-455a-8873-7972ecb85ca8",
    "status": "active",
    "quota_definition_url": "/v2/quota_definitions/52c5413c-869f-455a-8873-7972ecb85ca8",
    "spaces_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc/spaces",
    "domains_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc/domains",
    "private_domains_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc/private_domains",
    "users_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc/users",
    "managers_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc/managers",
    "billing_managers_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc/billing_managers",
    "auditors_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc/auditors",
    "app_events_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc/app_events"
  }
}
</pre>

In this example, the GUID of my-org is c54bf317-d791-4d12-89f0-b56d0936cfdc.

To make a private plan available to a specific organization, run:

`cf curl /v2/service_plan_visibilities -X POST -d '{"service_plan_guid":"SERVICE_PLAN_GUID","organization_guid":"ORG_GUID"}'`

Example:

<pre class="terminal">
$ cf curl /v2/service_plan_visibilities -X 'POST' -d '{"service_plan_guid":"1113aa0-124e-4af2-1526-6bfacf61b111","organization_guid":"aaaa1234-da91-4f12-8ffa-b51d0336aaaa"}'
    {
      "metadata": {
        "guid": "99993789-a368-483e-ae7c-ebe79e199999",
        "url": "/v2/service_plan_visibilities/99993789-a368-483e-ae7c-ebe79e199999",
        "created_at": "2014-02-12T21:03:42+00:00",
        "updated_at": null
      },
      "entity": {
        "service_plan_guid": "1113aa0-124e-4af2-1526-6bfacf61b111",
        "organization_guid": "aaaa1234-da91-4f12-8ffa-b51d0336aaaa",
        "service_plan_url": "/v2/service_plans/1113aa0-124e-4af2-1526-6bfacf61b111",
        "organization_url": "/v2/organizations/c54bf317-d791-4d12-89f0-b56d0936cfdc"
      }
    }
</pre>

Note: The `metadata` section of the JSON response to this command shows a service\_plan\_visibilities GUID. This GUID is used to [disable service plan visibility](#delete-plan-visibility).

Members of my-org can now see the plan-name-1 service plan in the list of available services when my-org is targeted. 

### <a id='delete-plan-visibility'></a>Disabling Plan Visibility ###

To remove access to a private service plan from an organization, run:

`cf curl /v2/service_plan_visibilities/SERVICE_PLAN_VISIBILITIES_GUID -X 'DELETE'`

Example:

<pre class="terminal">
$ cf curl /v2/service_plan_visibilities/99993789-a368-483e-ae7c-ebe79e199999 -X DELETE
</pre>