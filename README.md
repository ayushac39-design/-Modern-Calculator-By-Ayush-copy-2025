 let displayValue = '0';
        let firstOperand = null;
        let operator = null;
        let waitingForSecondOperand = false;
        
        const display = document.querySelector('.current-operand');
        const previousDisplay = document.querySelector('.previous-operand');
        
        // Update the display
        function updateDisplay() {
            display.textContent = displayValue;
        }
        
        // Handle number input
        function inputNumber(num) {
            if (waitingForSecondOperand) {
                displayValue = num;
                waitingForSecondOperand = false;
            } else {
                displayValue = displayValue === '0' ? num : displayValue + num;
            }
        }
        
        // Handle decimal point
        function inputDecimal() {
            if (waitingForSecondOperand) {
                displayValue = '0.';
                waitingForSecondOperand = false;
                return;
            }
            
            if (!displayValue.includes('.')) {
                displayValue += '.';
            }
        }
        
        // Handle operators
        function handleOperator(nextOperator) {
            const inputValue = parseFloat(displayValue);
            
            if (operator && waitingForSecondOperand) {
                operator = nextOperator;
                previousDisplay.textContent = `${firstOperand} ${operator}`;
                return;
            }
            
            if (firstOperand === null) {
                firstOperand = inputValue;
            } else if (operator) {
                const result = calculate(firstOperand, inputValue, operator);
                displayValue = `${parseFloat(result.toFixed(7))}`;
                firstOperand = result;
            }
            
            waitingForSecondOperand = true;
            operator = nextOperator;
            previousDisplay.textContent = `${firstOperand} ${operator}`;
        }
        
        // Perform calculation
        function calculate(first, second, op) {
            switch(op) {
                case '+': return first + second;
                case '-': return first - second;
                case '×': return first * second;
                case '÷': return first / second;
                case '%': return first % second;
                default: return second;
            }
        }
        
        // Reset calculator
        function resetCalculator() {
            displayValue = '0';
            firstOperand = null;
            operator = null;
            waitingForSecondOperand = false;
            previousDisplay.textContent = '';
        }
        
        // Delete last character
        function backspace() {
            if (displayValue.length > 1) {
                displayValue = displayValue.slice(0, -1);
            } else {
                displayValue = '0';
            }
        }
        
        // Add event listeners to buttons
        document.querySelectorAll('.number').forEach(button => {
            button.addEventListener('click', () => {
                if (button.textContent === '.') {
                    inputDecimal();
                } else {
                    inputNumber(button.textContent);
                }
                updateDisplay();
            });
        });
        
        document.querySelectorAll('.operator').forEach(button => {
            if (button.textContent !== '=') {
                button.addEventListener('click', () => {
                    handleOperator(button.textContent);
                    updateDisplay();
                });
            }
        });
        
        document.querySelector('.equals').addEventListener('click', () => {
            if (operator && !waitingForSecondOperand) {
                const inputValue = parseFloat(displayValue);
                const result = calculate(firstOperand, inputValue, operator);
                displayValue = `${parseFloat(result.toFixed(7))}`;
                previousDisplay.textContent = `${firstOperand} ${operator} ${inputValue} =`;
                firstOperand = null;
                operator = null;
                waitingForSecondOperand = true;
                updateDisplay();
            }
        });
        
        document.querySelector('.clear').addEventListener('click', () => {
            resetCalculator();
            updateDisplay();
        });
        
        document.querySelector('.backspace').addEventListener('click', () => {
            backspace();
            updateDisplay();
        });
        
        // Keyboard support
        document.addEventListener('keydown', event => {
            if (event.key >= '0' && event.key <= '9') {
                inputNumber(event.key);
                updateDisplay();
            } else if (event.key === '.') {
                inputDecimal();
                updateDisplay();
            } else if (['+', '-', '*', '/', '%'].includes(event.key)) {
                let op = event.key;
                if (op === '*') op = '×';
                if (op === '/') op = '÷';
                handleOperator(op);
                updateDisplay();
            } else if (event.key === 'Enter' || event.key === '=') {
                event.preventDefault();
                document.querySelector('.equals').click();
            } else if (event.key === 'Escape' || event.key === 'Delete') {
                resetCalculator();
                updateDisplay();
            } else if (event.key === 'Backspace') {
                backspace();
                updateDisplay();
            }
        });
      
        updateDisplay();
