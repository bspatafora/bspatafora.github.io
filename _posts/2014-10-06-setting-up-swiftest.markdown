---
layout: post
title:  "Setting up Swiftest"
date:   2014-10-06
---

  1. Add [Swiftest][] as a Git submodule of your repository by running `git submodule add git@github.com:Swiftest/Swiftest.git` from the top level of your project directory
      * Or, if you don’t have SSH set up: `git submodule add https://github.com/Swiftest/Swiftest.git`
  2. Install the Swiftest templates by running `cd Swiftest && ./etc/install_templates` from that directory
  3. Add the Swiftest project (`Swiftest/src/Swiftest/Swiftest.xcodeproj`) to your workspace
      1. Right click some empty space in the project tree sidebar, then select “Add Files to YourProject”
      2. Find and select `Swiftest.xcodeproj`
      3. Ensure your project is checked in the “Add to targets” box, then click Add
  4. Add a Swiftest runner target (the target you’ll use to run your tests) by going to File > New > Target...
      1. Select Swiftest iOS Suite or Swiftest OS X Suite
      2. Hit Next
      3. Name the target something like “YourProjectSpecs”
         * Don’t be like me and name it “Swiftest” or you’ll create a naming collision with the Swiftest target that exists in the Swiftest project, which you added to your project in step 3
      4. Make sure you’re adding it to your project (not the Swiftest project) before clicking Finish
  5. Add Swiftest as a target dependency of the target you created in step 4
      * Select your project in the project navigator
      * The project’s settings should appear in the main panel; select your Swiftest runner target from the sidebar
      * Navigate to the Target Dependencies section of the Build Phases tab, then click the + button and add the Swiftest target

  * To verify everything worked, switch to your Swiftest runner target, and hit command + r to run it
      * If everything is set up correctly, the iOS Simulator will launch, and you should see empty (0 passed, 0 failed, 0 pending) test output in the Debug area
      * You probably want to delete the ProjectNameTests directory that was generated when you originally created the project (if you haven’t already)
  * When adding project files, make sure to add them to both the YourProject target **and** the YourProjectSpecs target
      * When adding test files, only add them to the YourProjectSpecs target

[Swiftest]: https://github.com/Swiftest/Swiftest