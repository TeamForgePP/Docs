### Для (GET) api/user/home

``` python
class SprintMap(BaseModel):
    id: UUID             
    name: str
    seq: int
    deadline: date


class AllowedActions(BaseModel):
    can_delete: bool
    can_leave: bool


class Project(BaseModel):
    id: UUID                 
    name: str
    is_complited: bool
    current_sprint_name: str
    current_sprint_seq: int
    role: Literal[
        "team_lead",
        "backend",
        "frontend",
        "devops",
        "manager",
        "product_manager",
        "business_analyst",
        "curator",
    ]
    nearest_deadline: date
    sprint_map: List[SprintMap]
    allowed_actions: AllowedActions


class ProjectsResponse(BaseModel):
    projects: List[Project]
```

---

### BasicResponse

```python
class BasicResponse(BaseModel):
    success: bool
    message: str

```