
### Для (POST) api/user/profile/edit

```python
class EditProfileRequest(BaseModel):
    first_name: str
    last_name: str
    patronymic: str
    email: EmailStr
```