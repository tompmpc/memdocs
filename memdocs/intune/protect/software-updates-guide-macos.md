---
title: Admin checklist for macOS software updates in Microsoft Intune
description: Guidance and advice for administrators that create and manage software updated for macOS devices using Microsoft Intune.
author: Smritib17
ms.author: smbhardwaj
manager: dougeby
ms.date: 08/08/2023
audience: ITPro
ms.topic: how-to
ms.service: microsoft-intune
ms.subservice: protect
ms.localizationpriority: high

# optional metadata

#ROBOTS:

ms.reviewer: ahamil, rogerso, beflamm, mandia
ms.suite: ems
ms.custom: intune-azure
ms.collection:
- tier1
- M365-identity-device-management
---

# Software updates planning guide for managed macOS devices in Microsoft Intune

Keeping your devices current with updates is critical. Admins must do what they can to reduce risk of security events, and reduce this risk with minimal disruption to the business & users.

Intune has built-in policies that can manage software updates. For macOS devices, you can use Intune to manage device updates, configure when devices are updated, and review the device update status. 

This article includes an admin guide for enrolled and managed macOS devices. Use this information to help manage software updates on your organization-owned devices.

This article applies to:

- macOS 12.4+ devices enrolled in Intune

> [!TIP]
> If your devices are personally owned, then go to the [software updates admin guide for personal devices](software-updates-guide-personal-byod.md).

## Before you begin

To install updates faster and avoid delays, make sure the devices are:

- Powered on
- Plugged in
- Connected to Internet
- Not shut down but can be can a sleep state

## ✔️ Manage updates with policies

It's recommended you create policies that update your devices. It's not recommended to put this responsibility on end users.

By default, users receive notifications and/or see the latest updates available on their devices (Settings > General > Software Updates). Users can choose to download and install updates whenever they want.

They can also change the update behavior using the Automatic Updates feature on the device (Settings > Software Updates):

:::image type="content" source="./media/software-updates-guide-macos/default-update-settings-macos-device.png" alt-text="The operating system default update settings and controls on a macOS Apple device.":::

When users install their own updates (instead of admins managing the updates), it can disrupt user productivity and business tasks. For example:

- Users can apply updates that the business hasn't approved for use. This situation can cause issues with application compatibility or changes to the operating system or user experience that disrupt device use.

- Users can avoid applying updates that are required for security or app compatibility reasons. This delay can leave the devices at risk and/or prevent them from being able to function.

- Users can disable checking for new updates entirely.

Because of these potential issues, Microsoft recommends that you evaluate your use case scenarios and deploy policies to manage the update experience to minimize risk and disruption to your business.

## Admin steps for organization owned devices

To update macOS devices owned by your organization, Microsoft recommends the following steps. These steps apply to most macOS devices. You can also use these steps as a starting point for your own update strategy.

1. **[Create a managed software update policy](#-step-1---use-a-software-update-policy-to-manage-when-updates-are-installed)**: This policy forces updates to be downloaded and installed at a convenient time. Depending on the settings you enter, users aren't prompted and don't need to be using the device when the updates are installed.

2. **[Create a Settings Catalog software update policy](#-step-2---use-a-settings-catalog-policy-to-automatically-install-updates)**: This policy prevents end users from disabling update checks. It also configures the device to check for updates and prompt users regularly.

3. **[Configure and deploy Nudge](#-step-3---consider-using-the-nudge-community-tool)**: This community tool prompts users quickly when an update is available. If the first two policies don't motivate end users to install updates, then Nudge reminds them.

This section focuses on these steps.

### ✔️ Step 1 - Use a software update policy to manage when updates are installed

This Intune policy manages **when** updates are installed.

For example, you can manage when critical updates and firmware updates are installed. You can also manage how many times the user can defer an update before it's force installed.

For most organizations, Microsoft recommends you configure the following settings:

- **Update policy behavior settings**

  - **Critical updates**: Install later
  - **Firmware updates**: Install later
  - **Configuration file updates**: Install later
  - **All other updates (OS, built-in apps)**: Install later
    - **Maximum user deferrals**: 5
    - **Priority**: High

  > [!NOTE]
  >
  > - On recent macOS builds, almost all updates show as **Configuration data files** or **All other updates**. The **All other updates** settings are mostly legacy updates for older builds of macOS.
  > - The time specified in these settings is used by the Intune service. The time isn't the local device time. Be aware of time differences when you configure a maintenance window, espeically for a global environment.

- **Update policy schedule settings**

  - **Schedule type**: Update at next check-in

You can change the values to your preferred scheduled times. Some of the values may only affect minor updates, and not major updates. For the specific steps, and more information on these settings & their values, go to [Manage macOS software update policies in Intune](../protect/software-updates-macos.md).

With these settings, this policy locks these settings so users can't change them. The policy also:

1. Checks for updates each time the device checks in with the Intune service. If there are updates available, then they're automatically downloaded.

2. The device finds a time period when the device isn't being used.

    - If the device isn't being used, then the policy tries to automatically install the update.
    - If the device is being used, then end users can choose to install the update, or defer the installation up to five times. Be sure to encourage your end users to install updates when they're available.

    The following images show the prompts that end users can see when updates are available:

    :::image type="content" source="./media/software-updates-guide-macos/required-managed-update-sample-notification-macos.png" alt-text="The sample notification prompt for a required update on a macOS Apple device.":::

    :::image type="content" source="./media/software-updates-guide-macos/updates-available-sample-notification-macos.png" alt-text="The sample notification that an update is available on a macOS Apple device.":::

3. If end users use all the deferrals, then the update is force installed. For a forced installation, a restart doesn't prompt the end user, and could result in data loss.

### ✔️ Step 2 - Use a settings catalog policy to automatically install updates

This Intune policy manages **how** updates are installed.

For example, you can configure the device to automatically install updates, including app updates, when they're available.

This settings catalog policy works with [Step 1 - Use a software update policy to manage when updates are installed](#-step-1---use-a-software-update-policy-to-manage-when-updates-are-installed) (in this article). It makes sure the devices are checking for updates and prompting users to install them. End users still need to take action to finish the installation.

In your settings catalog policy, Microsoft recommends the following settings:

1. In the settings picker, search for **software update** and select **System Updates > Software Update**.
2. **Select all these settings** and close the settings picker.
3. Configure the following settings:

    - **Allow Pre Release Installation**: False
    - **Automatic Download**: True
    - **Automatically Install App Updates**: True
    - **Critical Update Install**: True
    - **Restrict Software Update Require Admin To Install**: False
    - **Config Data Install**: True
    - **Automatically Install MacOS Updates**: True
    - **Automatic Check Enabled**: True

This policy locks these settings so users can't change them. On the device, the software update settings are greyed out:

:::image type="content" source="./media/software-updates-guide-macos/update-settings-with-settings-catalog-policy-macos.png" alt-text="The software update settings are greyed out after the Intune settings catalog update policy applies to a macOS Apple device.":::

For more information on the settings catalog, including how to create a settings catalog policy, go to [Use the settings catalog to configure settings](../configuration/settings-catalog.md).

### ✔️ Step 3 - Consider using the Nudge community tool

This tool is optional, and can help you **manage the end user experience**.

A popular tool within the Microsoft macOS admin community is Nudge. [Nudge is an open source tool](https://github.com/macadmins/nudge) that encourages end users to install macOS updates. It provides a rich configuration experience for admins.

When Nudge is configured and deployed, end users see the following sample message when their device is ready to be updated. End users can also choose to update the device or defer the update:

:::image type="content" source="./media/software-updates-guide-macos/nudge-sample-notification-message-macos.png" alt-text="A sample Nudge community tool message when a software update is available a macOS Apple device." lightbox="./media/software-updates-guide-macos/nudge-sample-notification-message-macos.png":::

There's also a [sample script and Intune configuration policy](https://github.com/microsoft/shell-intune-samples/tree/master/macOS/Apps/Nudge) for Nudge in the Microsoft shell script repository. This script includes everything you need to get started with Nudge. Make sure you update the `.mobileconfig` file with your values.

## Use built-in reporting for update status

After the update policies are deployed, in the [Microsoft Intune admin center](https://go.microsoft.com/fwlink/?linkid=2109431), you can use the reporting feature to check the status of the updates.

For each device, you can see its current state of updates (Devices > macOS > select a device > Update policies for macOS):

:::image type="content" source="./media/software-updates-guide-macos/intune-report-device-update-category-status.png" alt-text="Use the built-in reporting to check the update status of a macOS Apple device in the Microsoft Intune admin center." lightbox="./media/software-updates-guide-macos/intune-report-device-update-category-status.png":::

## Next steps

- [Software updates planning guide for BYOD and personal devices in Microsoft Intune](software-updates-guide-personal-byod.md)
- [Software updates planning guide for managed Android Enterprise devices in Microsoft Intune](software-updates-guide-android.md)
- [Software updates planning guide and scenarios for supervised iOS/iPadOS devices in Microsoft Intune](software-updates-guide-ios-ipados.md)
