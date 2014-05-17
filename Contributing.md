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
- Bonus points :) - is the code "green" using the default ReSharper analyses?

**Don't add a demo/sample for your sink to the main solution.** Instead, use the [serilog-generator](https://github.com/serilog/serilog-generator) to push test data through your DLL. Ask on the discussion group if you need help with this.

If you do want to write a sample showing off your creation, add it to the [serilog-samples](https://github.com/serilog/serilog-samples) repository instead.
