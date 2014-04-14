Use this checklist to make sure your pull request is easy to integrate.

Adding an assembly/project:

- Is the assembly correctly named?
- Is the default namespace of the assembly `Serilog` with appropriate folders under that?
- Is XML documentation generation enabled for all configurations?
- Are all warnings treated as errors for all configurations?
- Are details in the NuSpec correct?
- Does the Release configuration build, as well as Debug?
- Does each file include the license header?
- If all dependencies are signed, is the assembly signed?