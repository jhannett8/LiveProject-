# Live Project

## Introduction

For the last two weeks of my time at the Tech Academy, I worked with my peers in a team format to develop a full scale MVC Web Application in C#.
I was tasked to fix bugs, clean up code, and add requested features to the existing program. 
There were some big changes made that could have been a large time sink, but we used what we had to deliver what was needed within the existing timeframe. 
Through this experience, I saw how a team of developers worked together to produce a quality product. 
Over the two week sprint I also had the opportunity to work on some other project management and team programming [skills](#Other-Skills-Learned) that I'm confident I will use again and again on future projects.

Feel free to navigate through this file as needed: 
[Application Overview](#C#-Application-Overview)
[BackEnd Stories](#Back-End-Stories)
[FullStack Stories](#Full-Stack-Stories)
[FrontEnd Stories](#Front-End-Stories)
[Skills](#Other-Skills-Learned)

  

## C# Application Overview 
The software application had been designed to manage a collection of jobs for our client. 
Admins will be able to create and distribute a weekly schedule assigning users to certain jobs. 
Users will be able to keep track of which job they are assigned to for the week. 
The main dashboard will display all of this information, and be mobil freindly. 
The primary components of this project include the creation of registered users, differentiation between users and admins, creation of "Jobs" with necessary details, adding users to those jobs with an instance of "Schedule" for each user on each job. 
The main display will show a "Master Schedule" that shows all the jobs and the users assigned to them. 
The secondary components include a Chat feature, for all users to have a single main chat room for discussion, and Company News, where admins can create announcements for all employees to read.

Below are descriptions of the stories I worked on, along with code snippets and navigation links. 
I also have some full code files in this repo for the larger functionalities I implemented.



## Back End Stories

### Story Overview: Adding DateTimeValidation  
We have custom validation for our DateTime objects. It was implemented before several of our classes were designed. 
It was not designed to be flexible about what types of DateTime objects would be acceptable. For instance, if we wanted to have an actual time associated with an object, and not just a Date, it wouldn't work properly. Several of our classes needed to remove the custom validation to get past errors. It was also instituted before we were using date pickers to set Dates, and it doesn't always work well with the format the date picker uses (yyyy-MM-dd). We needed to modify this validation, aka refactor, so it stops creating so many issues. I was tasked to look through the various classes that use a date time object (Schedule, Job Other, Calendar, Job Action, etc), and figure out what the needs of each of those classes are, and allow for validation that works with all these classes. 


      Validation/DateTimeModalBinder.cs    
      ...
      namespace ManagementPortal.Validation
      {
          public class DatetimeModelBinder : DefaultModelBinder
          {
              private string[] _dateFormats = { "MM/dd/yyyy", "YY/MM/DD", "DD/MM/YY", "dddd, dd MMMM yyyy",
                                                "MM/dd/yyyy hh:mm tt", "MM/dd/yyyy h:mm tt", "HH:mm", "yyyy-MM-dd",
                                                "hh:mm tt", "H:mm", "h:mm tt", "dddd", "HH", "H", "hh", "h"};

              public DatetimeModelBinder(string[] dateFormats)
              {
                  _dateFormats = dateFormats;
              }

              public override object BindModel(ControllerContext controllerContext, ModelBindingContext bindingContext)
              {
                  DateTime dateValue = new DateTime();
                  var value = bindingContext.ValueProvider.GetValue(bindingContext.ModelName);
                  bool success = DateTime.TryParseExact(value.AttemptedValue, _dateFormats, CultureInfo.InvariantCulture,                                 DateTimeStyles.None, out dateValue);

                  if (value.AttemptedValue != "")
                  {
                      if (success)
                      {
                          return dateValue;
                      }
                      else
                      {
                          throw new Exception(value.AttemptedValue + " is not a valid date");
                      }
                  }
                  else return null;

              }
          }
      }



## Full Stack Stories 

### Story Overview: Printer Bug Fix

After a large merge, our printer icon had been placed back to it's previous position; at the footer of the page.  Its functionality is had even been screwed up. Instead of using the existing icon, I had been tasked to utilize the icon from font-awesome, which would be congruent to the style of other icons on the navbar. 
Moreover, I had been tasked to study the code inside the HomeController and figure out how to fix its functionality. 
For this story I came up with an easy built-in browser solution that would enable the user to print the current screen, bypassing the messy code within the HomeController. 


    Views/Shared/Layout.cshtml
       <li>
          <a href="#" title="Print" onclick="window.print()"> <i class="fa fa-print"></i><text class="nav-title">Print</text></a>
      </li>
      



### Story Overview: Adding External Login extension

Modern web application allow for users to sign in with external authentication services which include several OAuth/OpenID and social media authentication services: Microsoft Accounts, Twitter, Facebook, and Google.
Our website is also able to integrate such services. 
So we'll allow certain users to sign in using their Google Account this time. 
I had been tasked to implement Google Sign-In configuration in our web application.
As well as do some front end work on the Account/Login page.

    App_Start/Startup.Auth.cs

      app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
                {
                    ClientId = "201664973530-ijolfogstoj3lciff7jmpnd0k8pt2erg.apps.googleusercontent.com",
                    ClientSecret = "XJykZ0BrTD3ow298Z1qpR-Jw"
                });
            

    Login.cshtml
    
    <h2>Log In</h2>
    <div class="row">
        <div class="col-md-12" id="loginContainer">
            <section id="loginForm" class="formContainer">
                @using (Html.BeginForm("Login", "Account", new { ReturnUrl = ViewBag.ReturnUrl }, FormMethod.Post, new { role = "form" }))
                {
                    @Html.AntiForgeryToken()
                    @Html.ValidationSummary(true, "", new { @class = "text-danger" })
                    <div class="form-group">
                        @Html.LabelFor(m => m.UserName, new { @class = "col-md-12 control-label" })
                        <div class="col-md-12">
                            @Html.TextBoxFor(m => m.UserName, new { @class = "form-control" })
                            @Html.ValidationMessageFor(m => m.UserName, "", new { @class = "text-danger" })
                        </div>
                    </div>
                    <div class="form-group">
                        @Html.LabelFor(m => m.Password, new { @class = "col-md-12 control-label" })
                        <div class="col-md-12">
                            @Html.PasswordFor(m => m.Password, new { @class = "form-control" })
                            @Html.ValidationMessageFor(m => m.Password, "", new { @class = "text-danger" })
                        </div>
                    </div>

                    <div align="center">
                        <div class="row justify-content-center">
                            <div class="loginItemContainer col-md-6">
                                @Html.CheckBoxFor(m => m.RememberMe)
                                @Html.LabelFor(m => m.RememberMe)
                            </div>
                            <div class="form-group loginItemContainer col-md-6">
                                <input type="submit" value="Log in" class="btn loginItemBtn darkbluetext" />
                            </div>
                        </div>

                        <div class="row justify-content-center">
                            <div class="loginItemContainer col-md-6">
                                @Html.ActionLink("Register as a new user", "NewUser", "Account", null, new { @class = "loginItemAnchor" })
                            </div>
                            <div class="loginItemContainer col-md-6">
                                @Html.ActionLink("Forgot your password?", "ForgotPassword", null, new { @class = "loginItemAnchor" })
                            </div>
                        </div>


                        <div class="form-group">
                            <div class="loginItemContainer col-md-6">
                                @Html.ActionLink("Back", null, null, new { Href = Request.UrlReferrer, @class = "loginItemAnchor" })
                            </div>
                        </div>

                        <div class="row justify-content-center">
                            <div class="col-md fbItemContainer">
                                <input type="submit" value="Login with Facebook" class="btn loginItemBtn" />
                            </div>
                            <div class="col-md twitterItemContainer">
                                <input type="submit" value="Login with Twitter" class="btn loginItemBtn" />
                            </div>
                            <div class="col-md googleItemContainer"> 
                                <input type="submit" value="Login with Google+" class="btn loginItemBtn" />
                            </div>
                        </div>
                    </div>
                }
            </section>
        </div>
    </div>


## Front End Stories

### Story Overview: Gradient Hover Effects 
The buttons on the login page already had a hover effect. But we wanted to test this gradient color Hover Effect. 

    Views/Shared/Login.cshtml
    @using ManagementPortal.Models
    @model LoginViewModel

    @{
        ViewBag.Title = "Log in";
        ViewBag.ReturnUrl = "home/dashboard";
    }

    <h2>Log In</h2>
    <div class="row">
        <div class="col-md-12" id="loginContainer">
            <section id="loginForm" class="formContainer">
                @using (Html.BeginForm("Login", "Account", new { ReturnUrl = ViewBag.ReturnUrl }, FormMethod.Post, new { role = "form" }))
                {
                    @Html.AntiForgeryToken()
                    @Html.ValidationSummary(true, "", new { @class = "text-danger" })
                    <div class="form-group">
                        @Html.LabelFor(m => m.UserName, new { @class = "col-md-12 control-label" })
                        <div class="col-md-12">
                            @Html.TextBoxFor(m => m.UserName, new { @class = "form-control" })
                            @Html.ValidationMessageFor(m => m.UserName, "", new { @class = "text-danger" })
                        </div>
                    </div>
                    <div class="form-group">
                        @Html.LabelFor(m => m.Password, new { @class = "col-md-12 control-label" })
                        <div class="col-md-12">
                            @Html.PasswordFor(m => m.Password, new { @class = "form-control" })
                            @Html.ValidationMessageFor(m => m.Password, "", new { @class = "text-danger" })
                        </div>
                    </div>

                    <div align="center">
                        <div class="row justify-content-center">
                            <div class="loginItemContainer col-md-6">
                                @Html.CheckBoxFor(m => m.RememberMe)
                                @Html.LabelFor(m => m.RememberMe)
                            </div>
                            <div class="form-group loginItemContainer col-md-6">
                                <input type="submit" value="Log in" class="btn loginItemBtn darkbluetext" />
                            </div>
                        </div>

                        <div class="row justify-content-center">
                            <div class="loginItemContainer col-md-6">
                                @Html.ActionLink("Register as a new user", "NewUser", "Account", null, new { @class = "loginItemAnchor" })
                            </div>
                            <div class="loginItemContainer col-md-6">
                                @Html.ActionLink("Forgot your password?", "ForgotPassword", null, new { @class = "loginItemAnchor" })
                            </div>
                        </div>


                        <div class="form-group">
                            <div class="loginItemContainer col-md-6">
                                @Html.ActionLink("Back", null, null, new { Href = Request.UrlReferrer, @class = "loginItemAnchor" })
                            </div>
                        </div>

                        <div class="row justify-content-center">
                            <div class="col-md fbItemContainer">
                                <input type="submit" value="Login with Facebook" class="btn loginItemBtn" />
                            </div>
                            <div class="col-md twitterItemContainer">
                                <input type="submit" value="Login with Twitter" class="btn loginItemBtn" />
                            </div>
                            <div class="col-md googleItemContainer"> 
                                <input type="submit" value="Login with Google+" class="btn loginItemBtn" />
                            </div>
                        </div>
                    </div>
                }
            </section>
        </div>
    </div>
    @section Scripts {
        @Scripts.Render("~/bundles/jqueryval")
    }



    site.css
    .loginItemContainer {
        background-image: linear-gradient(to right, #fbc2eb 0%, #a6c1ee 51%, #fbc2eb 100%);
        margin: 15px;
        padding: 0px 15px;
        box-shadow: 0 0 20px #9681c1; /*#a38fcc;*/
        max-width: 190px;
        width: 100%;
        line-height: 38px;
        border-radius: 5px;
        height: 38px;
        line-height: 38px;
        text-align: center;
        transition: 0.5s;
        flex: 1 1 auto;
        background-size: 200% auto;
    }
        .loginItemContainer:hover {
            background-position: right center; /* change the direction of the change here */
        }



### Story Overview: Bootstrap Cards
We wanted to see how the Job List (Job view index) would look like if we utilized the Bootstrap’s cards to contain the data. 
I had been tasked to replace the existing tables with bootstrap cards and to ensure any additions don't break any functionality to the search and filtering. 

    Views/Job/Index.cshtml
    <div class="row">
            @foreach (var item in Model)
            {
                <div class="col-3">
                    <div class="card rounded mt-4">
                        <div class="card-header">
                            <h4>@Html.DisplayFor(modelItem => item.JobTitle)</h4>
                            <p>
                                @Html.DisplayFor(modelItem => item.Location.SiteName)
                                @Html.DisplayFor(modelItem => item.Location.Address)
                                @Html.DisplayFor(modelItem => item.Location.Town)
                                @Html.DisplayFor(modelItem => item.Location.State)
                                @Html.DisplayFor(modelItem => item.Location.Zip)
                            </p>
                        </div>
                        <div class="card-body">
                            <table>
                                <tr>
                                    <td>Note: @Html.DisplayFor(modelItem => item.Details.Note) </td>
                                </tr>
                                <tr>
                                    <td>Active: @Html.DisplayFor(modelItem => item.Active)</td>
                                </tr>
                                <tr>
                                    <td>Job Type: @Html.DisplayFor(modelItem => item.JobType)</td>
                                </tr>
                                <tr>
                                    <td>Manager: @Html.DisplayFor(modelItem => item.Manager.FullName)</td>
                                </tr>
                                <tr>
                                    <td>Shift: @Html.DisplayFor(modelItem => item.WeeklyShifts.Default)</td>
                                </tr>
                                <tr>
                                    <td>@Html.Partial(AnchorButtonGroupHelper.PartialView, AnchorButtonGroupHelper.GetEditDetailsDelete(item.JobIb.ToString()))</td>
                                </tr>
                            </table>
                        </div>
                    </div>
                </div>
            }
        </div>

## Other Skills Learned
    •	Working with a group of developers to identify front and back end bugs to the improve usability of an application
    •	Improving project flow by communicating about who needs to check out which files for their current story
    •	Learning new efficiencies from other developers by observing their workflow and asking questions
    •	Practice with team programming/pair programming when one developer runs into a bug they cannot solve
