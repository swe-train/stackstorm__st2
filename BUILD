python_requirements(
    name="reqs",
    source="requirements-pants.txt",
    overrides={
        # flex and stevedore uses pkg_resources w/o declaring the dep
        ("flex", "stevedore"): dict(
            dependencies=[
                "//:reqs#setuptools",
            ]
        ),
        # do not use the prance[flex] extra as that pulls in an old version of flex
        "prance": dict(
            dependencies=[
                "//:reqs#flex",
            ]
        ),
        # tooz needs one or more backends (tooz is used by the st2 coordination backend)
        "tooz": dict(
            dependencies=[
                "//:reqs#redis",
                "//:reqs#zake",
            ]
        ),
        # make sure anything that uses st2-auth-ldap gets the st2auth constant
        "st2-auth-ldap": dict(
            dependencies=[
                "st2auth/st2auth/backends/constants.py",
            ]
        ),
    },
)

target(
    name="auth_backends",
    dependencies=[
        "//:reqs#st2-auth-backend-flat-file",
        "//:reqs#st2-auth-ldap",
    ],
)

python_test_utils(
    name="test_utils",
    skip_pylint=True,
)

file(
    name="license",
    source="LICENSE",
)

# this is used to quickly build a venv that includes all requirements and our wheels
for ic_name, ic in supported_python_interpreter_constraints().items():
    pex_binary(
        name=f"st2-{ic_name}",
        output_path=f"st2-{ic_name}.pex",
        interpreter_constraints=ic,
        dependencies=[
            # this should depend on all python_distribution targets
            "st2actions",
            "st2api",
            "st2auth",
            "st2client",
            "st2common",
            "st2reactor",
            "st2stream",
            "st2tests",
            "contrib/runners/action_chain_runner",
            "contrib/runners/announcement_runner",
            "contrib/runners/http_runner",
            "contrib/runners/inquirer_runner",
            "contrib/runners/local_runner",
            "contrib/runners/noop_runner",
            "contrib/runners/orquesta_runner",
            "contrib/runners/python_runner",
            "contrib/runners/remote_runner",
            "contrib/runners/winrm_runner",
        ],
        include_tools=True,  # include pex.tools to populate a venv from the pex
        include_sources=False,  # always includes generated wheels, so transitive sources not needed
        venv_hermetic_scripts=False,  # do not add -sE to script shebangs
    )
