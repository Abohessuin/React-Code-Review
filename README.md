# React-Code-Review
# ⚛️ **React Component Development Guidelines**

> A comprehensive guide for writing clean, maintainable, and scalable React components

**Version:** 1.0  
**Last Updated:** October 2025

---

## 📑 Table of Contents

1. [Mandatory Rules](#-mandatory-rules-must-follow)
2. [Nice to Have Best Practices](#-nice-to-have-recommended-best-practices)
3. [Quick Checklist](#-quick-checklist)
4. [Tools to Enforce Rules](#-tools-to-enforce-rules)


---

## 🚨 **MANDATORY RULES** (Must Follow)

### **1. Naming Conventions**

#### **1.1 Component Files**
```jsx
// ✅ CORRECT - PascalCase for components
UserProfile.jsx
QuotationHistoryTable.jsx
OrderStatusCard.jsx

// ❌ WRONG
userProfile.jsx
quotation-history-table.jsx
order_status_card.jsx
```

#### **1.2 Function Names**
```jsx
// ✅ CORRECT - camelCase, descriptive
const handleSubmit = () => {};
const fetchUserData = () => {};
const isAuthenticated = () => {};
const getFormattedDate = () => {};

// ❌ WRONG
const HandleSubmit = () => {};        // PascalCase for non-components
const fetchuserdata = () => {};       // Missing camelCase
const auth = () => {};                // Not descriptive
const GetDate = () => {};             // Wrong casing
```

#### **1.3 Boolean Variables & Selectors**
```jsx
// ✅ CORRECT - Use is/has/can prefix, proper camelCase
const isLoading = useState(false);
const hasPermission = checkAuth();
const canEdit = user.role === 'admin';
const selectIsAuthenticated = state => state.auth.isAuthenticated;
const selectIsShadowMode = state => state.ui.isShadowMode;

// ❌ WRONG
const loading = useState(false);       // Missing 'is' prefix
const selectisAuthenticated = ...;    // Wrong camelCase
const selectisShadowMode = ...;        // Wrong camelCase - 'Is' should be capitalized
```

**Why "Is" should be capitalized:**
- "Is" is a separate word in the selector name
- It's a boolean prefix indicating the return type
- Follows standard camelCase rules where each word after the first is capitalized
- Consistent with common naming patterns: `isLoading`, `isActive`, `isValid`

#### **1.4 Constants**
```jsx
// ✅ CORRECT - UPPER_SNAKE_CASE for constants
const API_ENDPOINT = '/api/v1/users';
const MAX_RETRY_ATTEMPTS = 3;
const DEFAULT_PAGE_SIZE = 10;

const STATUS_TYPES = {
  PENDING: 'PENDING',
  APPROVED: 'APPROVED',
  REJECTED: 'REJECTED',
};

const QUOTATION_STATUS = {
  OPEN: 'OPE',
  APPROVED: 'APP',
  CANCELLED: 'CAN',
  REJECTED: 'REJ',
};

// ❌ WRONG
const apiEndpoint = '/api/v1/users';   // Should be uppercase
const MaxRetry = 3;                     // Wrong casing
const status_types = { ... };          // Should be uppercase
```

---

### **2. Error Handling**

#### **2.1 API Calls Must Have Error Handling**
```jsx
// ✅ CORRECT
useEffect(() => {
  fetchUserData()
    .then(data => setUser(data))
    .catch(error => {
      console.error('Failed to fetch user:', error);
      setError('Unable to load user data');
      // Optional: Show toast notification
    })
    .finally(() => setLoading(false));
}, []);

// ✅ CORRECT - With async/await
useEffect(() => {
  const loadData = async () => {
    try {
      const data = await fetchUserData();
      setUser(data);
    } catch (error) {
      console.error('Failed to fetch user:', error);
      setError('Unable to load user data');
    } finally {
      setLoading(false);
    }
  };
  loadData();
}, []);

// ❌ WRONG - No error handling
useEffect(() => {
  fetchUserData().then(data => setUser(data));
}, []);
```

#### **2.2 Null/Undefined Checks**
```jsx
// ✅ CORRECT - Use optional chaining
const email = user?.profile?.email;
const city = user?.address?.city || 'Unknown';
const notifications = row.notifications?.notificationEmailTo;

// In JSX
<span>{user?.notifications?.emailTo}</span>

// ❌ WRONG - Can cause runtime errors
const email = user.profile.email;
const city = user.address.city;
const notifications = row.notifications.notificationEmailTo;
<span>{user.notifications.emailTo}</span>
```

#### **2.3 Error Boundaries**
```jsx
// ✅ CORRECT - Wrap risky components
<ErrorBoundary fallback={<ErrorMessage />}>
  <ComplexComponent />
</ErrorBoundary>

// ❌ WRONG - No error boundary for complex components
<ComplexComponent />
```

---

### **3. Props Validation**

#### **3.1 Always Define PropTypes or TypeScript**
```jsx
// ✅ CORRECT - PropTypes
import PropTypes from 'prop-types';

const UserCard = ({ userId, name, email, onEdit, isActive }) => {
  // ... component logic
};

UserCard.propTypes = {
  userId: PropTypes.string.isRequired,
  name: PropTypes.string.isRequired,
  email: PropTypes.string,
  onEdit: PropTypes.func,
  isActive: PropTypes.bool,
};

UserCard.defaultProps = {
  email: null,
  onEdit: null,
  isActive: false,
};

export default UserCard;

// ✅ CORRECT - TypeScript
interface UserCardProps {
  userId: string;
  name: string;
  email?: string;
  onEdit?: () => void;
  isActive?: boolean;
}

const UserCard: React.FC<UserCardProps> = ({ userId, name, email, onEdit, isActive }) => {
  // ... component logic
};

// ❌ WRONG - No validation
const UserCard = ({ userId, name, email, onEdit, isActive }) => { ... };
```

---

### **4. State Management**

#### **4.1 Use Proper State Initialization**
```jsx
// ✅ CORRECT
const [user, setUser] = useState(null);
const [isLoading, setIsLoading] = useState(false);
const [items, setItems] = useState([]);
const [error, setError] = useState(null);
const [count, setCount] = useState(0);

// ❌ WRONG
const [user, setUser] = useState();           // Undefined instead of null
const [loading, setLoading] = useState();     // No initial value
const [items, setItems] = useState();         // Should be []
```

#### **4.2 Don't Mutate State Directly**
```jsx
// ✅ CORRECT - Create new arrays/objects
setUsers([...users, newUser]);
setUser({ ...user, name: 'John' });
setItems(items.map(item => 
  item.id === id ? { ...item, selected: true } : item
));
setItems(items.filter(item => item.id !== id));

// ❌ WRONG - Direct mutation
users.push(newUser);
user.name = 'John';
items[0].selected = true;
items.splice(0, 1);
```

---

### **5. useEffect Rules**

#### **5.1 Always Specify Dependencies**
```jsx
// ✅ CORRECT - All dependencies listed
useEffect(() => {
  fetchData(userId, filters);
}, [userId, filters]);

// ❌ WRONG - Missing dependencies
useEffect(() => {
  fetchData(userId, filters);
}, []);  // ESLint will warn about missing dependencies
```

#### **5.2 Cleanup Side Effects**
```jsx
// ✅ CORRECT - Cleanup for API calls
useEffect(() => {
  const controller = new AbortController();
  
  fetchData(controller.signal)
    .then(setData)
    .catch(error => {
      if (error.name !== 'AbortError') {
        console.error(error);
      }
    });
  
  return () => controller.abort();
}, []);

// ✅ CORRECT - Cleanup for timers
useEffect(() => {
  const timer = setTimeout(() => {
    setMessage('Hello');
  }, 1000);
  
  return () => clearTimeout(timer);
}, []);

// ✅ CORRECT - Cleanup for event listeners
useEffect(() => {
  const handleResize = () => setWidth(window.innerWidth);
  window.addEventListener('resize', handleResize);
  
  return () => window.removeEventListener('resize', handleResize);
}, []);

// ❌ WRONG - No cleanup
useEffect(() => {
  fetchData().then(setData);
  setTimeout(() => setMessage('Hello'), 1000);
  window.addEventListener('resize', handleResize);
}, []);
```

---

### **6. Import Organization**

Organize imports in the following order with blank lines between groups:

```jsx
// ✅ CORRECT - Organized in groups

// 1. External libraries (React first)
import { useState, useEffect, useCallback, useMemo } from 'react';
import { useSelector, useDispatch } from 'react-redux';
import { useTranslation } from 'react-i18next';
import { Link } from 'react-router-dom';
import moment from 'moment';
import PropTypes from 'prop-types';

// 2. UI libraries (Material-UI, styled-components, etc.)
import Typography from '@mui/material/Typography';
import Button from '@mui/material/Button';
import Stack from '@mui/material/Stack';
import IconButton from '@mui/material/IconButton';

// 3. Local components (grouped by type)
import UserCard from './UserCard';
import LoadingSpinner from '../shared/LoadingSpinner';
import ErrorMessage from '../shared/ErrorMessage';

// 4. Utilities, Hooks, and API
import useAuth from '../../hooks/useAuth';
import useColumnVisibilityModel from '../../hooks/useColumnVisibilityModel';
import { formatDate } from '../../utils/dateHelpers';
import { getUserData, updateUser } from '../../api/users';

// 5. Redux selectors and actions
import { selectCurrentUser, selectIsAuthenticated } from '../../redux/auth/selectors';
import { loginUser, logoutUser } from '../../redux/auth/actions';

// 6. Assets (SVG, images)
import { ReactComponent as Logo } from '../../svg/logo.svg';
import { ReactComponent as EditIcon } from '../../svg/icon-edit.svg';

// 7. Types, Constants, and Configs
import { USER_ROLES, API_ENDPOINTS } from '../../utils/constants';
import { AUTH_PERMISSIONS } from '../../utils/authPermissions';

// ❌ WRONG - Random order, no grouping
import Button from '@mui/material/Button';
import { formatDate } from '../../utils/dateHelpers';
import { useState } from 'react';
import UserCard from './UserCard';
import { USER_ROLES } from '../../utils/constants';
import { useSelector } from 'react-redux';
```

---

### **7. Key Props**

```jsx
// ✅ CORRECT - Use unique, stable keys
{items.map(item => (
  <ListItem key={item.id}>
    {item.name}
  </ListItem>
))}

// ✅ CORRECT - Composite key when needed
{items.map(item => (
  <ListItem key={`${item.category}-${item.id}`}>
    {item.name}
  </ListItem>
))}

// ❌ WRONG - Using index as key (causes bugs with reordering, filtering)
{items.map((item, index) => (
  <ListItem key={index}>
    {item.name}
  </ListItem>
))}

// ❌ WRONG - Using non-unique keys
{items.map(item => (
  <ListItem key={item.type}>  // Multiple items can share the same type
    {item.name}
  </ListItem>
))}
```

---

### **8. Avoid Magic Values**

```jsx
// ✅ CORRECT - Use constants
const QUOTATION_STATUS = {
  OPEN: 'OPE',
  APPROVED: 'APP',
  CANCELLED: 'CAN',
  REJECTED: 'REJ',
};

const MAX_ITEMS_PER_PAGE = 10;
const DEBOUNCE_DELAY_MS = 300;
const API_TIMEOUT_MS = 5000;

const COLUMN_WIDTHS = {
  ACTIONS: 150,
  NAME: 200,
  EMAIL: 250,
  STATUS: 120,
};

// Use them
if (order.status === ORDER_STATUS.PENDING) { ... }
const items = data.slice(0, MAX_ITEMS_PER_PAGE);
setTimeout(callback, DEBOUNCE_DELAY_MS);

// ❌ WRONG - Magic strings/numbers
if (order.status === 'pending') { ... }  // Should use constant
const items = data.slice(0, 10);          // Why 10?
setTimeout(callback, 300);                // Why 300?
minWidth: 150                             // Why 150?
```

---

### **9. Accessibility (a11y)**

```jsx
// ✅ CORRECT - Proper accessibility attributes
<button onClick={handleClick} aria-label="Close dialog">
  <CloseIcon />
</button>

<img src={logo} alt="Company logo" />

<label htmlFor="email">Email Address</label>
<input id="email" type="email" aria-required="true" />

<nav aria-label="Main navigation">
  <ul>...</ul>
</nav>

// ✅ CORRECT - Keyboard navigation
<div 
  role="button" 
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => e.key === 'Enter' && handleClick()}
>
  Click me
</div>

// ❌ WRONG - Missing accessibility
<button onClick={handleClick}>
  <CloseIcon />  // No aria-label for icon-only button
</button>

<img src={logo} />  // No alt text

<label>Email Address</label>
<input type="email" />  // No id/htmlFor connection

<div onClick={handleClick}>Click me</div>  // Not keyboard accessible
```

---

### **10. Performance - Prevent Unnecessary Re-renders**

#### **10.1 useMemo for Expensive Computations**
```jsx
// ✅ CORRECT - Memoize expensive computations
const sortedItems = useMemo(
  () => items.sort((a, b) => a.name.localeCompare(b.name)),
  [items]
);

const filteredData = useMemo(
  () => data.filter(item => item.category === selectedCategory),
  [data, selectedCategory]
);

// ❌ WRONG - Recalculated on every render
const sortedItems = items.sort((a, b) => a.name.localeCompare(b.name));
const filteredData = data.filter(item => item.category === selectedCategory);
```

#### **10.2 useCallback for Functions Passed to Children**
```jsx
// ✅ CORRECT - Memoize callbacks
const handleDelete = useCallback(
  (id) => {
    deleteItem(id);
    refreshList();
  },
  [deleteItem, refreshList]
);

<ChildComponent onDelete={handleDelete} />

// ❌ WRONG - New function on every render
<ChildComponent onDelete={(id) => {
  deleteItem(id);
  refreshList();
}} />
```

#### **10.3 Avoid Unnecessary String Templates**
```jsx
// ✅ CORRECT - Only use template when needed
quoteId={row.quoteId}
userId={user.id}

// ❌ WRONG - Unnecessary string conversion
quoteId={`${row.quoteId}`}
userId={`${user.id}`}
```

---

## ✨ **NICE TO HAVE** (Recommended Best Practices)

### **1. Component Structure**

Follow this consistent order for better readability:

```jsx
// ✅ RECOMMENDED ORDER
import statements

// Constants (outside component)
const MAX_ITEMS = 10;
const STATUS_COLORS = { ... };

// Helper functions (outside component, pure functions)
const formatUserName = (user) => `${user.firstName} ${user.lastName}`;
const calculateTotal = (items) => items.reduce((sum, item) => sum + item.price, 0);

// PropTypes/Interfaces (optional: can be at bottom)
interface UserProfileProps { ... }

// Component definition
const UserProfile = ({ userId, onUpdate }) => {
  // 1. Hooks - State
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState(null);
  
  // 2. Hooks - Redux
  const dispatch = useDispatch();
  const currentUser = useSelector(selectCurrentUser);
  
  // 3. Hooks - Router
  const navigate = useNavigate();
  const { id } = useParams();
  
  // 4. Hooks - Custom
  const { t } = useTranslation();
  const isAuthenticated = useAuth();
  
  // 5. Hooks - useEffect
  useEffect(() => {
    fetchUser(userId);
  }, [userId]);
  
  // 6. Derived state - useMemo
  const fullName = useMemo(() => formatUserName(user), [user]);
  const sortedItems = useMemo(() => sortItems(user?.items), [user?.items]);
  
  // 7. Callbacks - useCallback
  const handleSave = useCallback(() => {
    onUpdate(user);
  }, [user, onUpdate]);
  
  const handleDelete = useCallback(() => {
    deleteUser(userId);
  }, [userId]);
  
  // 8. Helper functions (inside component, can access props/state)
  const validateForm = () => {
    return user?.email && user?.name;
  };
  
  // 9. Early returns (loading, error, empty states)
  if (isLoading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;
  if (!user) return <div>User not found</div>;
  
  // 10. Main render
  return (
    <div>
      <h1>{fullName}</h1>
      <button onClick={handleSave}>Save</button>
      <button onClick={handleDelete}>Delete</button>
    </div>
  );
};

// PropTypes (if at bottom)
UserProfile.propTypes = {
  userId: PropTypes.string.isRequired,
  onUpdate: PropTypes.func,
};

UserProfile.defaultProps = {
  onUpdate: null,
};

// Export
export default UserProfile;
```

---

### **2. Extract Complex Logic**

#### **2.1 Custom Hooks for Reusable Logic**
```jsx
// ✅ RECOMMENDED - Extract to custom hook
// hooks/useUserData.js
const useUserData = (userId) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [userId]);
  
  return { user, loading, error };
};

// In component
const UserProfile = ({ userId }) => {
  const { user, loading, error } = useUserData(userId);
  
  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;
  
  return <div>{user.name}</div>;
};

// ❌ NOT RECOMMENDED - All logic in component
const UserProfile = ({ userId }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  useEffect(() => {
    setLoading(true);
    fetchUser(userId)
      .then(setUser)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [userId]);
  
  // ... 200 more lines of component logic
};
```

#### **2.2 Utility Functions for Complex Calculations**
```jsx
// ✅ RECOMMENDED
// utils/dateFormatters.js
export const formatDateTime = (date) => 
  new Date(date).toLocaleString('en-US', {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit',
    hour: '2-digit',
    minute: '2-digit'
  });

export const formatDate = (date) => 
  new Date(date).toLocaleDateString('en-US', {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit'
  });

// In component
<Typography>{formatDateTime(order.createdAt)}</Typography>

// ❌ NOT RECOMMENDED - Repeated formatting logic
<Typography>
  {new Date(value).toLocaleString('en-US', { /* options */ })}
</Typography>
// ... same logic repeated 10 times in different places
```

---

### **3. Split Large Components**

```jsx
// ✅ RECOMMENDED - Split into smaller components

// ProductList.jsx
const ProductList = ({ products }) => (
  <div>
    <ProductListHeader />
    <ProductListFilters />
    <ProductGrid products={products} />
    <ProductListFooter />
  </div>
);

// ProductActionsCell.jsx
const ProductActionsCell = ({ product, canEdit }) => (
  <Stack direction="row" gap={1}>
    <ViewButton product={product} />
    {canEdit && (
      <>
        <EditButton product={product} />
        <DeleteButton product={product} />
      </>
    )}
  </Stack>
);

// ❌ NOT RECOMMENDED - One giant component (500+ lines)
const ProductList = ({ products }) => (
  <div>
    {/* 500 lines of JSX with all logic inline */}
  </div>
);

// RULE OF THUMB: 
// - Component > 200 lines? Consider splitting
// - Repeated JSX patterns? Extract to component
// - Complex cell renderers? Create separate components
```

---

### **4. Use Descriptive Variable Names**

```jsx
// ✅ RECOMMENDED - Clear and descriptive
const isUserAuthenticated = checkAuth();
const formattedDate = moment(date).format('DD/MM/YYYY');
const filteredActiveUsers = users.filter(u => u.isActive);
const quotationActionsColumn = createActionsColumn();
const selectedQuotationIds = [1, 2, 3];

// ❌ NOT RECOMMENDED - Cryptic abbreviations
const auth = checkAuth();
const d = moment(date).format('DD/MM/YYYY');
const u = users.filter(u => u.isActive);
const col = createActionsColumn();
const ids = [1, 2, 3];
```

---

### **5. Add Comments for Complex Logic**

```jsx
// ✅ RECOMMENDED - Explain WHY, not WHAT

/**
 * Fetches user preferences on component mount.
 * The preferences determine the default theme and language settings.
 * 
 * Note: We debounce this call to avoid hitting the API too frequently
 * if the component remounts rapidly.
 */
useEffect(() => {
  const timer = setTimeout(() => {
    getUserPreferences(userId)
      .then(prefs => setPreferences(prefs))
      .catch(err => {
        console.error('Failed to fetch preferences:', err);
        // Use default preferences if fetch fails
        setPreferences(DEFAULT_PREFERENCES);
      });
  }, DEBOUNCE_DELAY_MS);

  return () => clearTimeout(timer);
}, [userId]);

// Check if user has completed onboarding
// Users who haven't completed onboarding see a different dashboard
if (user.onboardingStatus === ONBOARDING_STATUS.COMPLETED) {
  return <FullDashboard />;
}

// ❌ NOT RECOMMENDED - No explanation or obvious comments
useEffect(() => {
  // Get preferences
  getUserPreferences(userId).then(prefs => setPreferences(prefs));
}, [userId]);

// Check status
if (user.onboardingStatus === 'completed') {
  return <FullDashboard />;
}
```

---

### **6. Consistent Styling Approach**

Choose ONE styling approach and use it consistently throughout the project:

```jsx
// ✅ RECOMMENDED - Option 1: sx prop (Material-UI)
<Box sx={{ 
  p: 2, 
  backgroundColor: 'primary.main',
  '&:hover': {
    backgroundColor: 'primary.dark',
  }
}}>
  Content
</Box>

// ✅ RECOMMENDED - Option 2: styled components
const StyledBox = styled(Box)(({ theme }) => ({
  padding: theme.spacing(2),
  backgroundColor: theme.palette.primary.main,
  '&:hover': {
    backgroundColor: theme.palette.primary.dark,
  },
}));

<StyledBox>Content</StyledBox>

// ✅ RECOMMENDED - Option 3: CSS modules
import styles from './UserCard.module.css';

<div className={styles.container}>
  <div className={styles.header}>Header</div>
</div>

// ❌ NOT RECOMMENDED - Mixing multiple approaches
<Box 
  sx={{ p: 2 }} 
  style={{ padding: '16px' }} 
  className="padding-2"
>
  Content
</Box>
```

---

### **7. Early Returns for Better Readability**

```jsx
// ✅ RECOMMENDED - Handle edge cases early
const UserProfile = ({ user }) => {
  // Handle edge cases first
  if (!user) return <div>No user found</div>;
  if (user.isDeleted) return <div>User deleted</div>;
  if (user.isBanned) return <div>User is banned</div>;
  
  // Main happy path
  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.email}</p>
      <UserActions user={user} />
    </div>
  );
};

// ❌ NOT RECOMMENDED - Deeply nested conditionals
const UserProfile = ({ user }) => {
  return (
    <div>
      {user ? (
        user.isDeleted ? (
          <div>User deleted</div>
        ) : user.isBanned ? (
          <div>User is banned</div>
        ) : (
          <div>
            <h1>{user.name}</h1>
            <p>{user.email}</p>
            <UserActions user={user} />
          </div>
        )
      ) : (
        <div>No user found</div>
      )}
    </div>
  );
};
```

---

### **8. Use Fragments to Avoid Extra DOM Nodes**

```jsx
// ✅ RECOMMENDED - Use fragments when no wrapper needed
return (
  <>
    <Header />
    <Content />
    <Footer />
  </>
);

// OR with key prop
return (
  <Fragment key={item.id}>
    <Header />
    <Content />
  </Fragment>
);

// ❌ NOT RECOMMENDED - Unnecessary wrapper div
return (
  <div>  {/* Extra DOM node */}
    <Header />
    <Content />
    <Footer />
  </div>
);
```

---

### **9. Avoid Inline Functions in JSX (for performance)**

```jsx
// ✅ RECOMMENDED - Extract to useCallback
const handleClick = useCallback(() => {
  doSomething();
  updateState();
}, [doSomething, updateState]);

<Button onClick={handleClick}>Click</Button>

// ⚠️ ACCEPTABLE - Simple one-liners
<Button onClick={() => setOpen(true)}>Open</Button>
<Button onClick={() => console.log('clicked')}>Log</Button>

// ❌ NOT RECOMMENDED - Complex logic inline (causes re-renders)
<Button onClick={() => {
  const data = processData(items);
  const validated = validateData(data);
  sendToServer(validated);
  showNotification('Success');
  updateUI();
}}>
  Submit
</Button>
```

---

### **10. Use Semantic HTML**

```jsx
// ✅ RECOMMENDED - Semantic HTML5 elements
<article>
  <header>
    <h1>{title}</h1>
    <time dateTime={publishDate}>{formattedDate}</time>
  </header>
  
  <section>
    <p>{content}</p>
  </section>
  
  <footer>
    <button>Share</button>
    <button>Like</button>
  </footer>
</article>

<nav aria-label="Main navigation">
  <ul>
    <li><a href="/home">Home</a></li>
    <li><a href="/about">About</a></li>
  </ul>
</nav>

<main>
  <section>...</section>
</main>

<aside>
  <h2>Related Articles</h2>
</aside>

// ❌ NOT RECOMMENDED - Div soup
<div>
  <div>
    <div>{title}</div>
    <div>{formattedDate}</div>
  </div>
  
  <div>
    <div>{content}</div>
  </div>
  
  <div>
    <div>Share</div>
    <div>Like</div>
  </div>
</div>
```

---

### **11. Consistent Conditional Rendering**

```jsx
// ✅ RECOMMENDED - Use consistent patterns

// Pattern 1: Logical AND for simple conditionals
{isLoading && <LoadingSpinner />}
{error && <ErrorMessage error={error} />}
{user && <UserCard user={user} />}

// Pattern 2: Ternary for if/else
{isLoading ? <LoadingSpinner /> : <Content />}
{user ? <UserProfile user={user} /> : <LoginPrompt />}

// Pattern 3: Early return for multiple conditions
if (isLoading) return <LoadingSpinner />;
if (error) return <ErrorMessage error={error} />;
if (!data) return <EmptyState />;

return <Content data={data} />;

// ❌ NOT RECOMMENDED - Inconsistent mixing
{isLoading && <LoadingSpinner />}
{error ? <ErrorMessage error={error} /> : null}
{user !== null && user !== undefined && <UserCard user={user} />}
```

---

### **12. Extract Column Definitions**

For large table components:

```jsx
// ✅ RECOMMENDED - Separate file for columns
// userTableColumns.js
export const createUserTableColumns = (formatDate, canEdit) => [
  {
    field: 'actions',
    headerName: 'Actions',
    renderCell: (params) => <UserActionsCell {...params} canEdit={canEdit} />,
  },
  {
    field: 'name',
    headerName: 'Name',
    width: 200,
  },
  {
    field: 'email',
    headerName: 'Email',
    width: 250,
  },
  {
    field: 'createdAt',
    headerName: 'Created',
    renderCell: (params) => formatDate(params.value),
  },
  // ... more columns
];

// UserTable.jsx
import { createUserTableColumns } from './userTableColumns';

const UserTable = () => {
  const columns = useMemo(
    () => createUserTableColumns(formatDate, canEdit),
    [formatDate, canEdit]
  );
  
  return <DataGrid columns={columns} />;
};

// ❌ NOT RECOMMENDED - 100+ lines of column definitions inline
const UserTable = () => {
  const columns = [
    { /* 100+ lines of column definitions */ }
  ];
  
  return <DataGrid columns={columns} />;
};
```

---

## 📋 **Quick Checklist**

Before submitting code for review, verify:

### **Mandatory (Must Pass):**
- [ ] ✅ All naming conventions followed
  - [ ] Components are PascalCase
  - [ ] Functions/variables are camelCase
  - [ ] Boolean prefixes (is/has/can) with proper casing
  - [ ] Constants are UPPER_SNAKE_CASE
- [ ] ✅ Error handling on all API calls (try/catch or .catch())
- [ ] ✅ Null/undefined checks with optional chaining (`?.`)
- [ ] ✅ PropTypes or TypeScript interfaces defined
- [ ] ✅ useEffect dependencies are correct
- [ ] ✅ No magic strings/numbers (use named constants)
- [ ] ✅ Keys are unique and stable (not array index)
- [ ] ✅ Accessibility attributes (aria-label, alt, htmlFor, etc.)
- [ ] ✅ No state mutations (use spread operators)
- [ ] ✅ Imports are organized by groups

### **Nice to Have (Recommended):**
- [ ] ✨ Component is < 200 lines (consider splitting if larger)
- [ ] ✨ Complex logic extracted to custom hooks
- [ ] ✨ Callbacks memoized with `useCallback`
- [ ] ✨ Expensive computations memoized with `useMemo`
- [ ] ✨ Comments added for complex/non-obvious logic
- [ ] ✨ Early returns for edge cases
- [ ] ✨ Semantic HTML used where appropriate
- [ ] ✨ Consistent styling approach
- [ ] ✨ No unnecessary inline functions
- [ ] ✨ Descriptive variable names

---

## 🚀 **Tools to Enforce Rules**

### **1. ESLint Configuration**

Create or update `.eslintrc.json`:

```json
{
  "extends": [
    "react-app",
    "react-app/jest",
    "plugin:react/recommended",
    "plugin:react-hooks/recommended",
    "plugin:jsx-a11y/recommended"
  ],
  "plugins": ["react", "react-hooks", "jsx-a11y"],
  "rules": {
    "react-hooks/rules-of-hooks": "error",
    "react-hooks/exhaustive-deps": "warn",
    "react/prop-types": "error",
    "react/jsx-key": "error",
    "no-console": ["warn", { "allow": ["warn", "error"] }],
    "prefer-const": "error",
    "no-var": "error"
  },
  "settings": {
    "react": {
      "version": "detect"
    }
  }
}
```

### **2. Prettier Configuration**

Create `.prettierrc`:

```json
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "es5",
  "printWidth": 100,
  "tabWidth": 2,
  "useTabs": false,
  "arrowParens": "avoid",
  "endOfLine": "lf"
}
```

### **3. Husky Pre-commit Hook**

In `package.json`:

```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}
```

Install:
```bash
npm install --save-dev husky lint-staged
npx husky install
```

### **4. VS Code Settings**

Create `.vscode/settings.json`:

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    "typescript",
    "typescriptreact"
  ],
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```


**Remember:** These guidelines exist to make our codebase more maintainable, not to slow you down. When in doubt, prioritize the **Mandatory** rules and use common sense for the rest. Happy coding! 🚀
