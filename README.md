# ProvisionGenie

🚨 Watch out - this is still under construction 🚨

**Guidance & Provisioning for Microsoft Teams**

ProvisionGenie lets you learn how Microsoft Teams works in general and how you can make it for your team. As Teams is a platform that can connect to a lot of services, we want to make your start even easier. We will skill you up and guide you through some questions regarding how your Team should look like. As a result, you can request the 'Team of your dreams', which will be provisioned automatically for you.

The core components of this solution:

* Power Apps Canvas App 
* 5 DataVerse tables
* 5 Azure Logic Apps flows

## Power Apps Canvas App

### High level overview on what the Canvas App does: 
* Short upskilling nuggets in Pop Ups
* Questionnaire to get information on 
  * Teams Name, Teams Description and logged in User to provision the Team itself
  * channels 
  * SharePoint list columns to provision this SharePoint list
  * SharePoint library columns to provision this SharePoint library
  * if Owner additionally wants a SharePoint list for taskmanagement and a welcome package
* Patch 5 Dataverse tables with the information we got by user

### Screens and their basic concepts:

##### Colors

1. Set variables **onStart** for the color you use the most - strongly recommended to follow Teams UI Toolkit here. For example:

`Set(color_blurple,ColorValue("#6264A7"))` and 
`Set(color_bg,ColorValue("#F5F5F5"))`

This way, you can refer to these values - or change them, if needed, more easily. 

##### Navigation
1. Create a `_selectedScreen` variable as a record containing row (number), title (text) and image (image) and create `NavigationMenu` collection .onStart, wrap both in `Concurrent()`:

``` Concurrent(
    Set(
        _selectedScreen,
        {
            Row: 1,
            Title: "Welcome",
            Image: ic_fluent_home_48_regular
        }
    ),
    ClearCollect(
        NavigationMenu,
        {
            Row: 1,
            Title: "Welcome",
            Image: ic_fluent_home_48_regular
        },
        {
            Row: 2,
            Title: "Teams",
            Image: ic_fluent_people_32_regular
        },
        {
            Row: 3,
            Title: "Channels",
            Image: ic_fluent_text_bullet_list_square_24_regular
        },
        {
            Row: 4,
            Title: "Libraries",
            Image: ic_fluent_document_48_regular
        },
        {
            Row: 5,
            Title: "Lists",
            Image: ic_fluent_clipboard_bullet_list_ltr_20_regular
        },
        {
            Row: 6,
            Title: "Checkout",
            Image: ic_fluent_checkbox_checked_24_regular
        }
    )
```

(To make this work replace the name of the images with the images you uploaded 💡
2. Create a gallery
with 

* Rectangle
* Textlabel
* Image

3. Set **Items** of the gallery to `NavigationMenu`
4. Set **TemplateFill** to `If(ThisItem.Row = _selectedScreen.Row, RGBA(220, 220, 220, 1), RGBA(0,0,0,0))`
5. Set **OnSelect** to 

```
Set(
    _selectedScreen,
    ThisItem
);
If(
    ThisItem.Row = 1,
    Navigate(
        'Welcome Screen',
        None
    ),
    ThisItem.Row = 2,
    Navigate(
        'Teams Screen',
        None
    ),
    ThisItem.Row = 3,
    Navigate(
        'Channel Screen',
        None
    ),
    ThisItem.Row = 4,
    Navigate(
        'Libraries Screen',
        None
    ),
    ThisItem.Row = 5,
    Navigate(
        'Lists Screen',
        None
    ),
    ThisItem.Row = 6,
    Navigate(
        'Checkout Screen',
        None
    )
)
```
7. Set **Visible** of the rectangle to `ThisItem.Row = _selectedScreen.Row`
8. Set **Text** of the TextLabel to `ThisItem.Title`
9. Set **Image** of the Image to `ThisItem.Image`

> Keep in mind to always `Set(_selectedScreen,{Title: "your screenname", <rownumber>})`in addition to `Navigate(your screenname)` if you want to let the user navigate to another screen not using the navigation gallery. 

##### SidePanel

The SidePanel consists if 2 tabs, **Details** and **Resources** and we use 

* 2 Textlabels for the tab names
* 2 Rectangles as an underline for the tab names
* at least 2 **HTMLText** controls to display content depending on the tab

1. Set the **HTMLText** of HTML text control to 
```
"
<div style='margin: 0 0 0 20px; font-size: 11pt !important; font-weight: lighter; color: #252525; padding: 0 10px; width: 100%; overflow: hidden;'>
   
  
    <div style=""float: left; width: 20px; text-align: right; margin: 0 20px 0 0; font-weight: bold;"" >*</div>
    <div style=""float: left; width: 75%; "">
        Step 1 <br><br>
    </div>
    <div style=""clear: both""></div>
  
    <div style=""float: left; width: 20px; text-align: right; margin: 0 20px 0 0; font-weight: bold;"" >*</div>
    <div style=""float: left; width: 75%; "">
      Step 2<br><br>
    </div>
    <div style=""clear: both""></div>
  
    <div style=""float: left; width: 20px; text-align: right; margin: 0 20px 0 0; font-weight: bold;"" >*</div>
    <div style=""float: left; width: 75%; "">
       Step 3<br><br>
    </div>
    <div style=""clear: both""></div>

    <div style=""float: left; width: 20px; text-align: right; margin: 0 20px 0 0; font-weight: bold;"" >*</div>
    <div style=""float: left; width: 75%; "">
       Step 4<br><br> 
    </div>
        <div style=""clear: both""></div>

    <div style=""float: left; width: 20px; text-align: right; margin: 0 20px 0 0; font-weight: bold;"" >*</div>
    <div style=""float: left; width: 75%; "">
        Step 5<br> 
    </div>
      <div style=""clear: both""></div>

    <div style=""float: left; width: 20px; text-align: right; margin: 0 20px 0 0; font-weight: bold;"" >*</div>
    <div style=""float: left; width: 75%; "">
       Step 6<br> 
    </div>

"
```
2. repeat with different text in the second HTML text control
3. Set **OnSelect** of the `Details` Textlabel to `UpdateContext({IsShowResourcesTab: false})`
4. Set **OnSelect** of the `Resources` Textlabel to `UpdateContext({IsShowResourcesTab:true})`
5. Set **Visible** of the `Resources` HTMLText to `IsShowResourcesTab`
6. Set **Visible** of the `Resources` Rectangle to `IsShowResourcesTab`
7. Set **Visible** of the `Details` HTMLText to `!IsShowResourcesTab`
8. Set **Visible** of the `Details` Rectangle to `!IsShowResourcesTab`
9. Set **FontWeight** of `Details` textlabel to `If(!IsShowResourcesTab,FontWeight.Bold, FontWeight.Lighter)` 
10. Set **FontWeight** of `Resources` textlabel to `If(IsShowResourcesTab,FontWeight.Bold, FontWeight.Lighter)`

This way, the content of `Resources` gets visible once the `Details` content is non-visible and vice versa. Also, user switches between the content by selecting the respecting textlabels. Font-weight will switch from `lighter` to `bold` and Rectangle (that serves as an underline) will be visible once user selects a Textlabel

#### PopUp 

In the app, we make use of various PopUps, either to educate users abouy how to work in Microsoft Teams, or to explain somthing that users can request (like 'Welconme Package') or to indicate a success. Most PopUps contain 3 different pages, which means theat we need 3 times different content for them as well

Popups contain the following controls: 

##### content: 

* Image
* Textlabel that serves as a Title for this PopUp
* TextLabel that serces as the main content for this PopUp

##### structure:

* Rectangle that serves as a Dimmer
* Button that serves as background for the PopUp
* Circles that serves as Stepper Dots so users can select them to navigate back and forth of the pages
* Cancel icon to close the PopUp
* an HTMLtext to create a shadow around the PopUp
* 2 Buttons for next/back
* 1 Rectangle to prettify the PopUp

###### Rectangle for Dimmer

purpose here is do create a lightbox effect and to dim everything but the PopUp itself. Following the Teams UI Toolkit: 

* Set **Fill** to `RGBA(37, 36, 35, 0.75)`
* Set size to the entire screen

###### Button that serves as background

* Set **Width** to `600`
* Set **Height** to `480`
* Set **Color** to `White`
* Set **Hover color** to `White`
* Set **Pressed color** to `White`
* Set **Border radius** to `3`

###### Circles that serves as Stepper Dots 

* Create 3 circles
* Set their **Width** to 8
* Set their **Height** to 8
* Align them horizontally
* Set **OnSelect** of Circle1 to `UpdateContext({isPage:1})`
* Set **OnSelect** of Circle2 to `UpdateContext({isPage:2})`
* Set **OnSelect** of Circle3 to `UpdateContext({isPage:3})`
* Set **Fill** of Circle1 to `If(isPage=1,color_blurple,color_bg)`
* Set **Fill** of Circle2 to `If(isPage=2,color_blurple,color_bg)`
* Set **Fill** of Circle3 to `If(isPage=3,color_blurple,color_bg)`

###### Cancel icon to close the PopUp

* Place your Cancel icon at the upper right hand corner of the button
* Set **OnSelect** to `UpdateContext({isShowPopUp: false})`

###### HTMLtext to create a shadow around the PopUp

To have a nice shadow around the PopUp

* Create an HTMLtext control
* Set its **HTMLtext** to `"<div style='margin:10px;width:600px;height:480px;background-color:#;box-shadow:0 3px 6px 1px  #252525; border-radius:3px'></div>"`
* Rearrange controls so that the shadow is underneath the button


###### next button

* Create a button
* Set colors as stated in Teams UI Toolkit if you like this to be design consistent to Teams, otherwise choose your own colors (preferably set them as variables)
* Set **OnSelect** to 
```
If(
    isPage= 1,
    UpdateContext({isPage: 2}),
    If(
        isPageTrack = 2,
        UpdateContext({isPage: 3}),
        UpdateContext({isShowPopUp: false})
    )
)
```
This way, users navigate to the next screen if they are on page 1 or 2 and close the PoPup if they are on page 3. 

* Set **Text** to `If(isPage=1,"yourtext-->2",If(isPage=2,"yourtext-->3","Close"))` This way, we display different texts depending on the page our user is currently at
* Set **Width** to `If(isPage=1,<value1>,If(isPage=2, <value2>,<value3>)` This way, the width of the button adjusts
* Set **X** to `If(isPage=1,<value1>,If(isPage=2, <value2>,<value3>)` to adjust horizontal position of the button. 

> To calculate this correctly, place the button by drag'n'drop where you want it. Now check the x-value, add the width to it. This is your target value. For the other pages, you will need to deduct the width of the button from that target value so you get the x-value of the button on that page. If you have more than 1 PopUp, it's worth to think about parameterizing this as well 

###### back button

* Create a button
* Set colors as stated in Teams UI Toolkit if you like this to be design consistent to Teams, otherwise choose your own colors (preferably set them as variables)
* Set **OnSelect** to 
```
If(
    isPage = 1,
    UpdateContext({isPage: 3}),
    If(
        isPage = 3,
        UpdateContext({isPageConversations: 2}),
        UpdateContext({isPageConversations:1})
    )
)
```
This way, users navigate to the previous screen. 


###### image

* Insert an image
* Set **Width** to `600`
* Set **Height** to `240`
* 
#### on all screens: 

##### Navigation

##### SidePanel
#### Welcome Screen

#### Teams Screen
#### Channels Screen
#### Library Screen
#### Lists Screen
#### Checkout Screen

### Variables

### Collections

## 5 DataVerse tables
//ToDo: describe relationships
## 5 Azure Logic Apps flows


* link to Deployment Guide
* link to Fact Sheet for Admins
* link to End User Quick Start Guide
* link to License

## Developers

![Carmen and Luise](https://github.com/LuiseFreese/ProvisionGenie/blob/main/media/Carmen_Luise.png)

This solution is an initiative by [Luise Freese](https://m365princess.com) and [Carmen Ysewijn](https://digipersonal.com/). 

## Contributions

We welcome contributions, we summarized how you can contribute in the [contribution guidelines](https://github.com/LuiseFreese/ProvisionGenie/blob/main/CONTRIBUTING.md). 

## Support us

💙 If you like this project, give it a ⭐ and share it with your friends!

![appreciation](https://github.com/LuiseFreese/ProvisionGenie/blob/main/media/undraw_Appreciation_re_p6rl.svg)

## Disclaimer

THIS CODE IS PROVIDED AS IS WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESS OR IMPLIED, INCLUDING ANY IMPLIED WARRANTIES OF FITNESS FOR A PARTICULAR PURPOSE, MERCHANTABILITY, OR NON-INFRINGEMENT.
