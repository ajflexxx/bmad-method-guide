# The Task vs Data Distinction: Why Techniques Are Data, Not Tasks

## The Core Distinction

You're absolutely right to question this - brainstorming techniques and elicitation methods *could* have been implemented as individual tasks. The decision to make them data rather than tasks reveals a fundamental design principle in BMad:

**Tasks are ORCHESTRATORS, Data items are TECHNIQUES**

## Why Techniques Are Data, Not Tasks

### 1. **Selection and Variability**
```
Data Pattern:
- Present 20 techniques as numbered list
- User selects by number
- Task applies the selected technique

If they were tasks:
- Would need 20+ separate task files
- How would user know which task to run?
- Would need a meta-task to list available tasks
```

### 2. **Execution Context**
```
As Data:
- ONE task (facilitate-brainstorming) maintains state
- Technique is applied within task context
- Task manages session flow, output capture, transitions

As Tasks:
- Each technique task would need to duplicate:
  - Session management
  - Output capture logic  
  - Transition handling
  - Document generation
```

### 3. **Lightweight vs Heavyweight**

**Data Items (Techniques):**
- Simple descriptions or instructions
- 3-5 lines each
- No complex logic
- Just "what to do"

**Tasks:**
- Multi-step procedures
- State management
- File operations
- Conditional logic
- Integration with other components

### 4. **The Selection Interface Pattern**

BMad uses a consistent pattern for user choice:
```
Task: "Which technique would you like?"
1. Technique A
2. Technique B
3. Technique C
...
9. Proceed

User: 2
Task: [Applies Technique B from data file]
```

This wouldn't work if each technique was a separate task - you'd need:
```
*task brainstorm-analogical-thinking
*task brainstorm-scamper
*task brainstorm-five-whys
... (20+ commands to remember!)
```

## The Design Logic

### Tasks Handle PROCESS
- Multi-step workflows
- State management
- File I/O operations
- Integration logic
- Complex orchestration

### Data Provides CONTENT
- Techniques to apply
- Methods to use
- Knowledge to reference
- Options to select from

## Real Examples

### facilitate-brainstorming-session.md (TASK)
**Process responsibilities:**
1. Session setup (ask context questions)
2. Present approach options
3. Manage technique selection
4. Execute techniques interactively
5. Capture output continuously
6. Handle session flow (warm-up → divergent → convergent → synthesis)
7. Generate structured document
8. Manage state between technique switches

### brainstorming-techniques.md (DATA)
**Content it provides:**
- Name of technique
- Brief instruction on how to apply it
- Example prompts or questions

The task ORCHESTRATES, the data INFORMS.

## Why This Design Makes Sense

### 1. **Reusability**
- One task can use many techniques
- Techniques can be shared across different tasks
- Easy to add new techniques (just add to data file)

### 2. **Maintainability**
- Techniques are centralized in one file
- Easy to update or add techniques
- No code duplication across multiple task files

### 3. **User Experience**
- Consistent selection interface
- Don't need to remember multiple commands
- Can browse available options easily

### 4. **Separation of Concerns**
- Task handles HOW (process, flow, state)
- Data provides WHAT (content, options, knowledge)

## The Exception Case

Note that BMad DOES have some "technique-like" tasks:
- `advanced-elicitation.md` - But this is an orchestrator that selects from elicitation methods
- `create-doc.md` - Generic processor for any template
- `execute-checklist.md` - Generic processor for any checklist

These are tasks because they:
1. Have complex orchestration logic
2. Manage significant state
3. Integrate with multiple components
4. Are reusable processors, not content

## The Decision Rule

**Make it a TASK if:**
- It has multiple steps that must be executed in order
- It needs to manage state between steps
- It performs file operations
- It orchestrates other components
- It's a reusable processor

**Make it DATA if:**
- It's a technique or method to apply
- It's knowledge to reference
- It's a set of options to choose from
- It's configuration or preferences
- It's lightweight instructions

## Alternative Design (What If?)

If techniques were tasks, the system would look like:

```
tasks/
  brainstorm-analogical-thinking.md
  brainstorm-scamper.md
  brainstorm-six-hats.md
  ... (20+ files)
  
  elicit-expand-contract.md
  elicit-critique-refine.md
  elicit-tree-of-thoughts.md
  ... (30+ files)
```

Problems:
- 50+ task files for techniques alone
- How does user discover them?
- How to present them as options?
- Massive duplication of session management code
- Harder to add new techniques
- No central place to see all available techniques

## The Genius of the Current Design

By making techniques data:
1. **One place to look** - All techniques in one file
2. **Easy selection** - Numbered list interface
3. **Simple addition** - Just add to the list
4. **Clean separation** - Process vs content
5. **Efficient reuse** - One task, many techniques
6. **Better UX** - User doesn't need to know task names

This is actually a beautiful example of the **Strategy Pattern** in software design - the task is the context, the techniques are interchangeable strategies, and the data file is the strategy repository.

## Conclusion

The line between tasks and data in BMad is:
- **Tasks** = Complex orchestration and process management
- **Data** = Content, techniques, and knowledge to be used BY tasks

This creates a clean, maintainable, and user-friendly system where adding new capabilities (techniques) doesn't require new code (tasks), just new content (data entries).