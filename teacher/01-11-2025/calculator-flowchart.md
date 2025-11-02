# Calculator Flowchart

This flowchart represents the logic flow of the calculator JavaScript code (lines 76-231).

```mermaid
flowchart TD
    Start([Button Clicked]) --> GetKey[Get button textContent as key]
    GetKey --> IsClear{key === 'C'?}

    IsClear -->|Yes| Clear[clearCalculator Function]
    Clear --> ResetVars[Reset all variables:<br/>firstNumber = ''<br/>secondNumber = ''<br/>operator = ''<br/>isEnteringSecondNumber = false<br/>lastKeyPressed = '']
    ResetVars --> ClearDisplay[Clear display:<br/>valueElement.innerHTML = ''<br/>resultElement.innerHTML = '0']
    ClearDisplay --> End([End])

    IsClear -->|No| HandleBtn[handleButtonPress Function]
    HandleBtn --> CheckType{What type is key?}

    CheckType -->|Number| HandleNum[handleNumberPress Function]
    CheckType -->|Operator| HandleOp[handleOperatorPress Function]
    CheckType -->|Equal '='| Calc[calculateResult Function]

    %% Number Press Flow
    HandleNum --> LastWasEqual{lastKeyPressed === '='?}
    LastWasEqual -->|Yes| ClearAndSet[clearCalculator<br/>firstNumber = key]
    LastWasEqual -->|No| CheckSecondNum{isEnteringSecondNumber?}
    CheckSecondNum -->|Yes| AddToSecond[secondNumber = secondNumber + key]
    CheckSecondNum -->|No| AddToFirst[firstNumber = firstNumber + key]
    ClearAndSet --> AddToScreen1[addToScreen key]
    AddToSecond --> AddToScreen1
    AddToFirst --> AddToScreen1
    AddToScreen1 --> UpdateLast1[lastKeyPressed = key]
    UpdateLast1 --> End

    %% Operator Press Flow
    HandleOp --> LastWasOp{isOperator lastKeyPressed?}
    LastWasOp -->|Yes| ReplaceOp[Replace operator on screen:<br/>Remove last char, add new key<br/>operator = key<br/>lastKeyPressed = key]
    ReplaceOp --> End
    LastWasOp -->|No| HasComplete{firstNumber && operator && secondNumber?}
    HasComplete -->|Yes| CalcFirst[calculateResult]
    HasComplete -->|No| CheckLast2{lastKeyPressed === '=' OR shouldCalculate?}
    CalcFirst --> CheckLast2
    CheckLast2 -->|Yes| ShowResultOp[valueElement.innerHTML = firstNumber + key]
    CheckLast2 -->|No| AddOpToScreen[addToScreen key]
    ShowResultOp --> SetOpState[operator = key<br/>isEnteringSecondNumber = true<br/>lastKeyPressed = key]
    AddOpToScreen --> SetOpState
    SetOpState --> End

    %% Calculate Result Flow
    Calc --> HasAllValues{firstNumber && secondNumber && operator?}
    HasAllValues -->|No| End
    HasAllValues -->|Yes| ConvertNums[Convert to numbers:<br/>num1 = Number firstNumber<br/>num2 = Number secondNumber]
    ConvertNums --> CheckOp{Which operator?}

    CheckOp -->|+| Add[result = num1 + num2]
    CheckOp -->|-| Subtract[result = num1 - num2]
    CheckOp -->|*| Multiply[result = num1 * num2]
    CheckOp -->|/| CheckZero{num2 === 0?}

    CheckZero -->|Yes| ShowError[resultElement.innerHTML = 'Error'<br/>clearCalculator]
    ShowError --> End
    CheckZero -->|No| Divide[result = num1 / num2]

    Add --> ShowResult[resultElement.innerHTML = result]
    Subtract --> ShowResult
    Multiply --> ShowResult
    Divide --> ShowResult

    ShowResult --> PrepNext[Prepare for next calculation:<br/>firstNumber = result.toString<br/>secondNumber = ''<br/>operator = ''<br/>isEnteringSecondNumber = false<br/>lastKeyPressed = '=']
    PrepNext --> End

    style Start fill:#e1f5ff
    style End fill:#ffe1f5
    style Clear fill:#fff4e1
    style HandleNum fill:#e1ffe1
    style HandleOp fill:#ffe1f5
    style Calc fill:#f5e1ff
    style ShowError fill:#ffcccc
```

## Key Functions Overview

### 1. **Button Click Handler** (lines 89-99)

- Sets up event listeners for all buttons
- Routes "C" button to `clearCalculator()`
- Routes all other buttons to `handleButtonPress()`

### 2. **clearCalculator()** (lines 102-110)

- Resets all state variables to empty strings/false
- Clears the display screens

### 3. **handleButtonPress()** (lines 129-140)

- Routes to appropriate handler based on button type:
  - Numbers → `handleNumberPress()`
  - Operators → `handleOperatorPress()`
  - Equals → `calculateResult()`

### 4. **handleNumberPress()** (lines 143-157)

- Handles logic for entering numbers
- Checks if starting fresh after "="
- Adds to firstNumber or secondNumber based on state
- Updates display and lastKeyPressed

### 5. **handleOperatorPress()** (lines 160-189)

- Replaces operator if last key was also an operator
- Calculates previous result if calculation is complete
- Updates display and sets operator state

### 6. **calculateResult()** (lines 192-230)

- Validates all required values exist
- Performs calculation based on operator
- Handles division by zero error
- Shows result and prepares state for next calculation

## State Variables

- `firstNumber`: Stores the first operand
- `secondNumber`: Stores the second operand
- `operator`: Stores the current operator (+, -, \*, /)
- `isEnteringSecondNumber`: Boolean flag indicating input phase
- `lastKeyPressed`: Tracks the last button pressed for context
