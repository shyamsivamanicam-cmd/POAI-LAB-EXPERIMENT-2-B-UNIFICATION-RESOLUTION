def apply_substitution(term, theta):
    while isinstance(term, str) and term in theta:
        term = theta[term]

    if isinstance(term, list):
        return [apply_substitution(x, theta) for x in term]

    return term


def unify(x, y, theta=None):
    if theta is None:
        theta = {}

    x = apply_substitution(x, theta)
    y = apply_substitution(y, theta)

    if x == y:
        return theta

    if isinstance(x, str) and x.islower():
        theta[x] = y
        return theta

    if isinstance(y, str) and y.islower():
        theta[y] = x
        return theta

    if isinstance(x, list) and isinstance(y, list) and len(x) == len(y):
        for a, b in zip(x, y):
            theta = unify(a, b, theta)
            if theta is None:
                return None
        return theta

    return None


# Knowledge base
facts = [["Human", "John"]]

# Rule: Human(x) -> Mortal(x)
rules = [
    (["Human", "x"], ["Mortal", "x"])
]

# Query: Is John mortal?
query = ["Mortal", "John"]
resolved = False

for antecedent, conclusion in rules:
    theta = unify(conclusion, query)

    if theta is not None:
        required_fact = apply_substitution(antecedent, theta)

        for fact in facts:
            if unify(required_fact, fact, theta.copy()) is not None:
                resolved = True
                break

    if resolved:
        break

if resolved:
    print("Query is resolved: John is Mortal")
else:
    print("Query could not be resolved")
