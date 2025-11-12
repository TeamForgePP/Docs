### Для (POST) api/user/home/new-project
```python
TeamRole = Literal[
    "team_lead",
    "backend",
    "frontend",
    "devops",
    "manager",
    "product_manager",
    "business_analyst",
    "curator",
]


class TeamMember(BaseModel):
    id: UUID
    roles: List[TeamRole]

class CreateProjectRequest(BaseModel):
    name: str
    description: Optional[str] = None
    team: List[TeamMember]
    git_organization: str

```