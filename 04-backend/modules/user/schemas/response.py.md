### Для (GET) api/user/profile

```python
class TeamRole(Enum): #импортируется из enums.py 
    Backend = "backend"  
    BusinessAnalyst = "business_analyst"  
    Curator = "Curator"  
    Devops = "devops"  
    Frontend = "frontend"  
    Manager = "manager"  
    ProductManager = "product_manager"  
    TeamLead = "team_lead"


class ProfileResponse(BaseModel):
    first_name: str
    last_name: str
    patronymic: str
    group: str
    role: TeamRole
    email: EmailStr
```

---

### BasicResponse

```python
class BasicResponse(BaseModel):
    success: bool
    message: str
```