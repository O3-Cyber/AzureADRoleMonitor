# AzureADRoleMonitor

## What is this project?

Azure AD Role Monitor is a small project that scans Azure AD built in roles, and automatically identifies sensitive or dangeorus roles based on the actions that the role is able to perform on an Azure AD tenant.

The project scans built in roles each night, and maps each action to known primitives. The results are provided in the "feed" directory of this project, timestamped whenever a change is detected or new primitives are introduced.

## Why is this project useful?

When performing security operations or security testing, it is often helpful to know what capabilities and attack techniques an adversary may have if they compromise a user with a specific role. This can prove difficult to keep track of because Azure AD roles may change without notice, or new sensitive roles may be introduced without notice.

This project automates the monitoring of these roles against known primitives, and provides a programmatically consumable feed of these dangerous roles. The feed may be used by both offensive and defensive teams to help identify the security sensitivity of user accounts.

### Data contents

The *feed/latest.json* file always contains data from our most recent scan, which is run nightly. This file contains a json list, where each list object is a dictionary with the following attributes:

**id**: id of the Azure AD built in role definition

**name**: canonical name of the Azure AD role

**primitives**: A list of actions that map to known primitives in our primitives.json file

## What is a "Tier"

The feed contains a list of primitives, which contain the primitive name, description, abusable role, and a "tier". The tier represents security sensitivity of the role, based on the abuse primitives that are available through its roles. For example, if a role is able to give itself global administrator, it is a highly sensitive role and should not be treated the same as a normal user account.

This tier mapping can be used by architects and security teams to support security requirements for different account types. For example, administrative users, or users with tier 0 permissions, may require additional monitoring compared to normal users with tier 2 permissions.

Our recommendation for tiering is based on the following high level principles:

**Tier 0**: Permissions that give a user broad and high privileged access to the directory, or that may often be used to elevate privileges to such a role

**Tier 1**: Permissions that give a user limited administrative access to a directory, and which are not often useful for privilege escalation to a Tier 0 permission.

**Tier 2**: Permissions that give a user very limited access to a directory, and which provide little to no paths for privilege escalation

**IMPORTANT: The tiers represented in this project are based on the priviled "actions" that are allowed on each role. Some roles that should be considered sensitive, such as *Azure AD Joined Device Local Administrator*, do not contain any actions that represent their privileged capabilities. This means that at this time, these roles will be automatically labelled as Tier 2 roles, despite their sensitivity**

Overall, we encourage users to evaluate and identify their own tier mapping for primitives and accounts based on their risk tolerance, and concerns about each primitive, but seek to provide a reasonable baseline for a tiering strategy at most organizations based on known abuse techniques.

## How do I consumed the data?

Our most current data will be contained in *feed/latest.json*. Tools that want to make use of this data may either clone this entire repository and watch that file, or create their automation by downloading the publicly available raw *feed/latest.json* file from Github.

## How do I contribute?

If you have primitives that you would like to contribute, disagree with our tiering, or would like to suggest a re-wording of something in the project, feel free to create a pull request and update the primitives.json files with your suggestions.