
### (GET) /api/projects/{project_id}
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


class ProjectTeamMember(BaseModel):
    id: UUID
    first_name: str
    last_name: str
    roles: List[TeamRole]


class ReportEditInfo(BaseModel):
    editor_id: Optional[UUID] = None
    editor_fn: Optional[str] = None
    editor_ln: Optional[str] = None
    updated_at: Optional[datetime] = None


class ProjectReport(BaseModel):
    id: UUID
    url: str              
    creator_id: UUID
    creator_fn: str
    creator_ln: str
    title: str
    description: str
    teacher_note: str
    edit: Optional[ReportEditInfo] = None


class AllowedActions(BaseModel):
    can_edit: bool
    can_finish: bool
    reports: bool


class ProjectDetailResponse(BaseModel):
    project_id: UUID
    project_name: str
    team: List[ProjectTeamMember]
    git: str
    description: str
    reports: List[ProjectReport]
    allowed_actions: AllowedActions

```