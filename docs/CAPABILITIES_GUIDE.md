# Copilot Coding Agent Capabilities Guide

This comprehensive guide demonstrates the five core capabilities of the Copilot coding agent with practical examples and best practices.

## Table of Contents
1. [Fix Bugs](#fix-bugs)
2. [Implement Incremental New Features](#implement-incremental-new-features)
3. [Improve Test Coverage](#improve-test-coverage)
4. [Update Documentation](#update-documentation)
5. [Address Technical Debt](#address-technical-debt)

---

## Fix Bugs

### Overview
The Copilot coding agent excels at identifying, diagnosing, and fixing bugs through systematic analysis and targeted solutions.

### Capabilities
- Analyze stack traces and error messages
- Trace code execution paths
- Identify root causes vs symptoms
- Implement minimal, targeted fixes
- Add tests to prevent regression

### Example Scenarios

#### Scenario 1: Null Pointer Exception
**Issue**: Application crashes when user data is missing

**Analysis Process**:
1. Review error: `TypeError: Cannot read property 'name' of undefined`
2. Trace variable assignment
3. Identify missing null check
4. Determine appropriate default behavior

**Fix**:
```javascript
// Before
function displayUserName(user) {
  return user.name.toUpperCase();
}

// After
function displayUserName(user) {
  if (!user || !user.name) {
    return 'Anonymous';
  }
  return user.name.toUpperCase();
}
```

**Test Added**:
```javascript
test('displayUserName handles null user', () => {
  expect(displayUserName(null)).toBe('Anonymous');
});

test('displayUserName handles user without name', () => {
  expect(displayUserName({})).toBe('Anonymous');
});
```

#### Scenario 2: Off-by-One Error
**Issue**: Loop processes one too many items

**Fix**:
```python
# Before (bug)
for i in range(0, len(items) + 1):
    process(items[i])  # IndexError on last iteration

# After (fixed)
for i in range(len(items)):
    process(items[i])
```

### Best Practices
- Make minimal changes
- Preserve existing behavior where correct
- Add tests for the bug scenario
- Document why the fix works
- Consider edge cases

---

## Implement Incremental New Features

### Overview
Add new functionality that integrates seamlessly with existing code while maintaining consistency and quality.

### Capabilities
- Understand existing patterns and architecture
- Implement features that fit naturally
- Follow established conventions
- Add proper error handling
- Create comprehensive documentation

### Example Scenarios

#### Scenario 1: Add User Authentication
**Requirement**: Add basic authentication to API endpoints

**Implementation Strategy**:
1. Review existing middleware pattern
2. Create authentication middleware
3. Add to relevant routes
4. Update documentation

**Code**:
```javascript
// New middleware following existing pattern
function authenticateUser(req, res, next) {
  const token = req.headers.authorization;
  
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Invalid token' });
  }
}

// Apply to routes
app.get('/api/protected', authenticateUser, (req, res) => {
  res.json({ message: 'Protected data', user: req.user });
});
```

#### Scenario 2: Add Data Validation
**Requirement**: Validate input data before processing

**Implementation**:
```python
def validate_user_data(data):
    """
    Validate user data before saving.
    
    Args:
        data: Dictionary containing user information
        
    Returns:
        Tuple of (is_valid, error_message)
    """
    if not data.get('email'):
        return False, "Email is required"
    
    if not re.match(r"[^@]+@[^@]+\.[^@]+", data['email']):
        return False, "Invalid email format"
    
    if len(data.get('password', '')) < 8:
        return False, "Password must be at least 8 characters"
    
    return True, None

# Usage in existing code
def create_user(user_data):
    is_valid, error = validate_user_data(user_data)
    if not is_valid:
        raise ValueError(error)
    
    # Proceed with user creation
    return save_user(user_data)
```

### Best Practices
- Keep changes focused and small
- Follow existing code style
- Add tests for new functionality
- Update documentation
- Consider backward compatibility

---

## Improve Test Coverage

### Overview
Enhance code reliability and maintainability through comprehensive, well-structured tests.

### Capabilities
- Identify untested code paths
- Write unit and integration tests
- Cover edge cases and errors
- Create maintainable test fixtures
- Follow testing best practices

### Example Scenarios

#### Scenario 1: Adding Unit Tests
**Target**: Function with no tests

**Code Under Test**:
```javascript
function calculateDiscount(price, discountPercent) {
  return price - (price * discountPercent / 100);
}
```

**Comprehensive Test Suite**:
```javascript
describe('calculateDiscount', () => {
  // Normal cases
  test('calculates 10% discount correctly', () => {
    expect(calculateDiscount(100, 10)).toBe(90);
  });
  
  test('calculates 50% discount correctly', () => {
    expect(calculateDiscount(200, 50)).toBe(100);
  });
  
  // Edge cases
  test('handles 0% discount', () => {
    expect(calculateDiscount(100, 0)).toBe(100);
  });
  
  test('handles 100% discount', () => {
    expect(calculateDiscount(100, 100)).toBe(0);
  });
  
  test('handles decimal prices', () => {
    expect(calculateDiscount(99.99, 15)).toBeCloseTo(84.99, 2);
  });
  
  // Boundary conditions
  test('handles very small prices', () => {
    expect(calculateDiscount(0.01, 10)).toBeCloseTo(0.009, 3);
  });
  
  test('handles large prices', () => {
    expect(calculateDiscount(1000000, 20)).toBe(800000);
  });
});
```

#### Scenario 2: Integration Tests
**Target**: API endpoint testing

**Test Implementation**:
```python
import unittest
from app import create_app

class UserAPITestCase(unittest.TestCase):
    def setUp(self):
        self.app = create_app('testing')
        self.client = self.app.test_client()
        
    def test_create_user_success(self):
        """Test successful user creation"""
        response = self.client.post('/api/users', json={
            'email': 'test@example.com',
            'password': 'securepass123'
        })
        self.assertEqual(response.status_code, 201)
        self.assertIn('id', response.json)
        
    def test_create_user_missing_email(self):
        """Test user creation fails without email"""
        response = self.client.post('/api/users', json={
            'password': 'securepass123'
        })
        self.assertEqual(response.status_code, 400)
        self.assertIn('error', response.json)
        
    def test_create_user_duplicate_email(self):
        """Test user creation fails with duplicate email"""
        # Create first user
        self.client.post('/api/users', json={
            'email': 'test@example.com',
            'password': 'pass1'
        })
        
        # Try to create duplicate
        response = self.client.post('/api/users', json={
            'email': 'test@example.com',
            'password': 'pass2'
        })
        self.assertEqual(response.status_code, 409)
```

### Best Practices
- Test behavior, not implementation
- Use descriptive test names
- Keep tests independent
- Use fixtures for common setup
- Test both success and failure paths
- Cover edge cases thoroughly

---

## Update Documentation

### Overview
Maintain clear, accurate, and helpful documentation that grows with your codebase.

### Capabilities
- Update README files
- Write API documentation
- Add inline comments judiciously
- Create usage examples
- Document configuration options
- Maintain changelogs

### Example Scenarios

#### Scenario 1: README Update
**Before**: Minimal README

**After**: Comprehensive README
```markdown
# Project Name

Brief description of what the project does.

## Features

- Feature 1: Description
- Feature 2: Description
- Feature 3: Description

## Installation

\`\`\`bash
npm install project-name
\`\`\`

## Quick Start

\`\`\`javascript
const Project = require('project-name');

const instance = new Project({
  apiKey: 'your-api-key',
  timeout: 5000
});

instance.doSomething()
  .then(result => console.log(result))
  .catch(error => console.error(error));
\`\`\`

## API Reference

### Constructor

\`\`\`javascript
new Project(options)
\`\`\`

**Options:**
- `apiKey` (string, required): Your API key
- `timeout` (number, optional): Request timeout in ms (default: 3000)
- `retries` (number, optional): Number of retries (default: 3)

### Methods

#### doSomething(params)

Performs the main operation.

**Parameters:**
- `params.input` (string): The input data
- `params.validate` (boolean): Whether to validate (default: true)

**Returns:** Promise<Result>

**Example:**
\`\`\`javascript
await instance.doSomething({
  input: 'data',
  validate: true
});
\`\`\`

## Configuration

Create a `.projectrc` file:

\`\`\`json
{
  "defaultTimeout": 5000,
  "enableCache": true
}
\`\`\`

## Troubleshooting

### Common Issues

**Error: "Connection timeout"**
- Increase the timeout value
- Check your network connection

**Error: "Invalid API key"**
- Verify your API key is correct
- Check it hasn't expired

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

## License

MIT
\`\`\`

#### Scenario 2: API Documentation
**Adding comprehensive function documentation**:

```python
def process_payment(amount, currency, payment_method, customer_id):
    """
    Process a payment transaction.
    
    This function handles payment processing through various payment methods,
    validates the transaction, and records it in the database.
    
    Args:
        amount (float): Payment amount, must be positive
        currency (str): Three-letter currency code (e.g., 'USD', 'EUR')
        payment_method (str): Payment method type 
            ('credit_card', 'debit_card', 'paypal')
        customer_id (str): Unique customer identifier
    
    Returns:
        dict: Transaction result containing:
            - transaction_id (str): Unique transaction identifier
            - status (str): Transaction status ('success', 'pending', 'failed')
            - timestamp (datetime): Transaction timestamp
            - message (str): Human-readable status message
    
    Raises:
        ValueError: If amount is negative or currency code is invalid
        PaymentError: If payment processing fails
        DatabaseError: If transaction recording fails
    
    Example:
        >>> result = process_payment(
        ...     amount=99.99,
        ...     currency='USD',
        ...     payment_method='credit_card',
        ...     customer_id='cust_123'
        ... )
        >>> print(result['status'])
        'success'
    
    Note:
        Payment processing is asynchronous for amounts over $1000.
        These transactions will return a 'pending' status initially.
    """
    # Implementation here
    pass
```

### Best Practices
- Keep documentation current with code
- Use clear, concise language
- Provide practical examples
- Document edge cases and limitations
- Include troubleshooting tips
- Version your documentation

---

## Address Technical Debt

### Overview
Improve code quality, maintainability, and performance through strategic refactoring and modernization.

### Capabilities
- Identify code smells
- Refactor complex code
- Remove duplication
- Update dependencies
- Improve architecture
- Enhance error handling

### Example Scenarios

#### Scenario 1: Removing Code Duplication
**Before**: Duplicated validation logic

```javascript
// User creation
function createUser(data) {
  if (!data.email) throw new Error('Email required');
  if (!data.email.includes('@')) throw new Error('Invalid email');
  if (!data.password) throw new Error('Password required');
  if (data.password.length < 8) throw new Error('Password too short');
  // ... create user
}

// User update
function updateUser(id, data) {
  if (data.email && !data.email.includes('@')) {
    throw new Error('Invalid email');
  }
  if (data.password && data.password.length < 8) {
    throw new Error('Password too short');
  }
  // ... update user
}
```

**After**: Extracted common validation

```javascript
// Extracted validators
const validators = {
  email: (email) => {
    if (!email) return 'Email required';
    if (!email.includes('@')) return 'Invalid email';
    return null;
  },
  
  password: (password) => {
    if (!password) return 'Password required';
    if (password.length < 8) return 'Password too short';
    return null;
  }
};

function validate(data, required = []) {
  const errors = [];
  
  for (const field of required) {
    if (validators[field]) {
      const error = validators[field](data[field]);
      if (error) errors.push(error);
    }
  }
  
  if (errors.length > 0) {
    throw new Error(errors.join(', '));
  }
}

// Simplified functions
function createUser(data) {
  validate(data, ['email', 'password']);
  // ... create user
}

function updateUser(id, data) {
  // Only validate provided fields
  const fieldsToValidate = Object.keys(data).filter(k => validators[k]);
  validate(data, fieldsToValidate);
  // ... update user
}
```

#### Scenario 2: Simplifying Complex Logic
**Before**: Nested conditions

```python
def calculate_price(item, quantity, customer_type, season):
    if customer_type == 'premium':
        if season == 'holiday':
            if quantity > 10:
                return item.price * quantity * 0.7
            else:
                return item.price * quantity * 0.8
        else:
            if quantity > 10:
                return item.price * quantity * 0.8
            else:
                return item.price * quantity * 0.9
    else:
        if season == 'holiday':
            if quantity > 10:
                return item.price * quantity * 0.85
            else:
                return item.price * quantity * 0.95
        else:
            return item.price * quantity
```

**After**: Simplified with strategy pattern

```python
DISCOUNT_RATES = {
    ('premium', 'holiday', True): 0.70,    # >10 items
    ('premium', 'holiday', False): 0.80,   # ≤10 items
    ('premium', 'regular', True): 0.80,
    ('premium', 'regular', False): 0.90,
    ('regular', 'holiday', True): 0.85,
    ('regular', 'holiday', False): 0.95,
    ('regular', 'regular', True): 1.00,
    ('regular', 'regular', False): 1.00,
}

def calculate_price(item, quantity, customer_type, season):
    """
    Calculate price with applicable discounts.
    
    Returns: Final price after discounts
    """
    bulk_order = quantity > 10
    key = (customer_type, season, bulk_order)
    discount_rate = DISCOUNT_RATES.get(key, 1.00)
    
    return item.price * quantity * discount_rate
```

#### Scenario 3: Improving Error Handling
**Before**: Generic error handling

```javascript
function fetchUserData(userId) {
  try {
    const user = database.getUser(userId);
    return user;
  } catch (e) {
    console.log('Error');
    return null;
  }
}
```

**After**: Specific error handling

```javascript
class UserNotFoundError extends Error {
  constructor(userId) {
    super(`User ${userId} not found`);
    this.name = 'UserNotFoundError';
    this.userId = userId;
  }
}

class DatabaseConnectionError extends Error {
  constructor(message) {
    super(`Database connection failed: ${message}`);
    this.name = 'DatabaseConnectionError';
  }
}

function fetchUserData(userId) {
  try {
    const user = database.getUser(userId);
    
    if (!user) {
      throw new UserNotFoundError(userId);
    }
    
    return user;
    
  } catch (error) {
    if (error instanceof UserNotFoundError) {
      // Log and rethrow for caller to handle
      logger.warn(`User lookup failed: ${error.message}`);
      throw error;
    }
    
    if (error.code === 'ECONNREFUSED') {
      throw new DatabaseConnectionError(error.message);
    }
    
    // Unexpected error
    logger.error('Unexpected error in fetchUserData:', error);
    throw error;
  }
}
```

### Best Practices
- Make refactoring changes separately from features
- Ensure tests pass before and after
- Refactor incrementally
- Document architectural decisions
- Update dependencies regularly
- Keep security in mind

---

## Conclusion

The Copilot coding agent's five core capabilities work together to maintain high-quality, well-tested, well-documented code with minimal technical debt. By following the patterns and best practices outlined in this guide, teams can maximize the effectiveness of AI-assisted development.

### Key Takeaways

1. **Bug Fixes**: Be systematic, minimal, and add tests
2. **New Features**: Integrate naturally, follow patterns, document well
3. **Test Coverage**: Be comprehensive, cover edge cases, keep tests maintainable
4. **Documentation**: Keep current, be clear, provide examples
5. **Technical Debt**: Refactor safely, simplify complexity, improve quality

### Getting the Most from Copilot Agent

- Provide clear context and requirements
- Use descriptive issue templates
- Review and test all changes
- Maintain consistent code standards
- Iterate and refine based on feedback

---

*This guide is maintained by the ApalanQ organization to help developers work effectively with AI-assisted development tools.*
