# MongoDB

## pymongo



## ODM

object document mapping, 对象文档映射. 类似于关系型数据库的ORM

### MongoDB的ODM

- PyMODM
- MongoEngine
- ...

## 基于MongoEngine的ODM

```python
from mongoengine import connect, Document, StringField, IntField, \
	EmbeddedDocument, FloatField, ListField, EmbeddedDocumentField

connect('student')

SEX_CHOICE = (
	('male', '男'),
	('female', '女')
)

class Grade(EmbeddedDocument):
	'''成绩'''
	course = StringField(required=True)
	score = FloatField(required=True)

class Student(Document):
	'''学生'''
	name = StringField(max_length=32, required=True)
	age = IntField(max_length=10)
	sex = StringField(choice=SEX_CHOICE, required=True)
	address = StringField()
	# grade = ListField(FloatField())
	grades = ListField(EmbeddedDocumentField(Grade))

	meta = {
		'collection': 'students',
		'ordering': ['-age']
	}

class OdmHelper(object)
	def get_one(self):
		'''查询一条数据'''
		return Student.objects.first()
	
	def get_many(self):
		'''查询多条数据'''
		return Student.objects.all()
	
	def get_from_oid(self):
		'''根据ID查询数据'''
		return Student.objects.filtet(id=1).first()
	
	def add_one(self):
		'''增加数据'''
		math = Grade(course='数学', score=95)
		english = Grade(xourse='英语', score=89)
		stu = Student(name='zhangsan', age=21, sex='male', grades=[math, english])
		stu.save()
		return stu
	
	def update(self):
		'''修改一条数据'''
		obj = Student.objects.filter(age=20).update_one(age=22)
		'''修改多条数据'''
		obj = Student.objects.filter(age=20).update(age=22)
		return obj
	
	def delete(self):
		'''删除数据'''
		obj = Student.objects.filter(sex='male').first().delete()
		obj = Student.objects.filter(sex='male').delete()

```