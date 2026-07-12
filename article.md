# Prompt Engineering an AI Tutor: How I Got AI to Teach Instead of Just Code

AI can write working code in seconds. That's exactly why understanding the fundamentals matters more than ever — not less.

If you don't understand *why* a piece of configuration works, you can't debug it when it breaks. You can't tell a genuinely good AI suggestion from a confident-sounding one. And you can't adapt generated code to a context the AI never anticipated. Fundamentals aren't the opposite of AI-assisted development — they're what make AI-assisted development actually reliable.

The real skill shift isn't "learn to code" vs. "learn to prompt." It's learning to use AI as an assistant that accelerates *understanding*, not one that replaces it. That means staying the person who reads, questions, runs, and owns the code — while AI explains trade-offs, surfaces edge cases, and speeds up the parts that don't require judgment.

So I decided to test that idea with a real prompt, not just a theory.

## The experiment: turning AI into a tutor, not a builder

I engineered a prompt that flips the usual dynamic. Instead of asking AI to generate a fully working setup for me, I designed it to act like a mentor sitting next to me — explaining, questioning, and pausing for approval at every step, while I stay the one typing every command.

The concrete example I built it for is close to home: **scaffolding a QA automation project with Playwright and TypeScript.** But the underlying pattern isn't QA-specific — it's a blueprint for turning any AI assistant into a tutor for any stack.

In this case, the AI:

- Explains each step (Node/npm compatibility, TypeScript config, ESLint/Prettier/Husky, TypeDoc, POM/COM base classes) before anything is run
- Never touches the project folder itself — every command and snippet is executed by me
- Raises "what if" scenarios at each step (ESM vs. CommonJS, running in CI, different Node targets) so the tradeoffs are understood, not just accepted
- Pauses for real teaching moments when I ask "why," instead of steamrolling through a checklist

The AI acts as a tutor. I remain the engineer.

## Try it yourself

Whether or not Playwright is your stack, the full prompt — and the reasoning behind its structure — is open on GitHub, in case it's useful as a template for your own "AI tutor" prompt:
🔗 <https://github.com/daniv/playwright-typescript-project-tutor>

If you're a QA Automation Engineer, SDET, or building something similar for a different stack, I'd genuinely value your take: does this strike the right balance between speed and understanding?

---
*#SoftwareTesting #Playwright #TypeScript #QAAutomation #PromptEngineering #AIAssistedDevelopment*