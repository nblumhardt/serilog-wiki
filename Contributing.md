Use this checklist to make sure your pull request is easy to integrate. **Please note:** ensure your PR targets the `dev` branch.

> **Important:** sinks are no longer accepted via pull requests, as each sink is now maintained in a separate repository. You may contribute a sink by creating a repository with the appropriate name yourself, then raising an issue to transfer ownership of it to the Serilog organisation. As an example, see [Serilog.Sinks.MongoDB](https://github.com/serilog/serilog-sinks-mongodb).

> "Extras" projects are no longer being accepted directly at all; instead we'd rather help you establish an independent org or repository for your project. Please raise an issue for help with this, too. As an example, see [SerilogMetrics](https://github.com/serilog-metrics/serilog-metrics).

> Discussion around this approach can be found at #344.


**

Creating a sink:

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
