
df.head()     # first 5 rows
df.tail()     # last 5 rows
df.sample(10) # 10 random rows

def create_employees_fast(num_employees):
    return pd.DataFrame([{
        "First Name": fake.first_name(),
        "Last Name": fake.last_name(),
        "Job": fake.job(),
        "Department": fake.random_element(["HR", "Marketing", "Finance"]),
        "Role": fake.random_element(["Manager", "Developer", "Analyst", "Associate"]),
        "Salary": fake.random_int(min=3000, max=150000, step=1000),
        "Email": fake.email()
    } for _ in range(num_employees)])


df = create_employees_fast(50000)
print(df.shape)   # should print (50000, 7)


df.to_csv("employees_50k.csv", index=False)

chunk_size = 10000
with open("employees_large.csv", "w") as f:
    for i in range(5):  # 5 chunks of 10k = 50k
        df_chunk = create_employees_fast(chunk_size)
        df_chunk.to_csv(f, header=(i==0), index=False, mode="a")

df = create_employees_fast(50000)
df.to_csv("employees_50k.csv", index=False)
print("Done! File saved.")
