# Introduction to YAML

## Introduction to YAML

Ansible playbooks are text files or configuration files the are writen in YAML format.

YAML files represent data, in this case, configuraton data.

## Dictionary X List X List of Dictionaries

- Dictionary: using for store different information or properties of a single object.

- List: using for store multiple items of the same type of object.

- List of Dictionaries: in case both previous examples happen.

## Exercise

### Q - 03

| Key/Property | Value |
|--------------|-------|
| name         | john  |
| gender       | male  |
| age          | 24    |

Resposta

```yaml
employee:
    name: john
    gender: male
    age: 24
```

### Q - 04

We would like to record information about multiple employees. Convert the dictionary employee to an array employees.

```yaml
employees:
    -
        name: john
        gender: male
        age: 24
    -
        name: sarah
        gender: female
        age: 28
```

## See Documentation

https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html
