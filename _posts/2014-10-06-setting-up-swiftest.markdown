---
layout: post
title:  "Setting up Swiftest"
date:   2014-10-06
---

  1. Clone [Swiftest][] into the your project’s “workspace” directory (the top-level directory where YourProject.xcodeproj lives)
  2. Install the Swiftest templates by running `cd Swiftest && ./etc/install_templates` from that directory
  3. Add the Swiftest project (`Swiftest/src/Swiftest/Swiftest.xcodeproj`) to your workspace
      1. Right click some empty space in the project tree sidebar, then select “Add Files to YourProject”
      2. Find and select `Swiftest.xcodeproj`
      3. Ensure your project is checked in the “Add to targets” box, then click Add
  4. Add Swiftest as a Target by going to File > New > Target...
      1. Select Swiftest iOS Suite or Swiftest OS X Suite
      2. Hit Next
      3. **WHATEVER YOU DO, DON’T USE “Swiftest” AS THE PRODUCT NAME**
          * I thought this was a great name
          * It’s actually a terrible name, because the Swiftest project (which you just added to your workspace) contains (unsurprisingly) a target called “Swiftest”
          * Creating another “Swiftest” target leads to nothing working due to the naming collision
          * Maybe call it “YourProjectSpecs”
          * Big thank you to Brian Pratt for taking the time to show me my mistake (oh, and for making Swiftest)
      4. Make sure you’re adding it to your project (not the Swiftest project) before clicking Finish
  5. To verify everything worked, switch to the target you just created, and hit command + r
      * If it did, the iOS Simulator should launch, and you should see empty (0 passed, 0 failed, 0 pending) test output in the Debug area
      * You probably want to delete the ProjectNameTests directory (unrelated to Swiftest) that was generated when you originally created the project (if you haven’t already)
  6. When adding project files, make sure to add them to both the YourProject target **and** the YourProjectSpecs target
      * When adding test files, only add them to the YourProjectSpecs target

[Swiftest]: https://github.com/Swiftest/Swiftest