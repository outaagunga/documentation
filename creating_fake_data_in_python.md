
```python
# import the libraries we need
from faker import Faker
import pandas as pd

# create an instance of Faker
fake = Faker(locale='en_US')

# Let's create a function
def create_employees(num_employees):
    # Let's create an empty list to add our employee dictionaries
    employee_list = []
    
    # Let's create an employees dictionary
    for i in range(num_employees):   # ensures we get exactly num_employees
        employee = {}
        employee['first_name'] = fake.first_name()
        employee['last_name'] = fake.last_name()
        employee['job'] = fake.job()
        employee['department'] = fake.random_element(elements=("IT", "HR", "Marketing", "Finance"))
        employee['role'] = fake.random_element(elements=("Manager", "Developer", "Analyst", "Associate"))
        employee['salary'] = fake.random_int(min=30000, max=150000, step=1000)
        employee['email'] = fake.email()
        
        employee_list.append(employee)
    
    return pd.DataFrame(employee_list)

# Generate 5000 fake employee records
data = create_employees(5000)

# Save to CSV
data.to_csv("employees.csv", index=False)

# Save to Excel
data.to_excel("employees.xlsx", index=False)
```
