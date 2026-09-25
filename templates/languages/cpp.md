# Language Template: C++

See `templates/TEMPLATE_RULES.md` for shared design principles (question-bank shape, using web
research mid-dialog).

Used by `workflow-init` when the user selects C++. Static rules apply once selected;
questions get asked and their answers become literal content in the project's `CLAUDE.md`.
Nothing here is written into `CLAUDE.md` verbatim except the static rules — questions exist
to produce project-specific text, not to be echoed as-is.

---

## Static Rules (apply unconditionally once C++ is selected)

- Code must compile with the chosen standard (see Q1) — no silent fallback to an older one.
- Favor explicit interfaces in headers; avoid hidden state.
- If a precompiled header (PCH) is in use (see Q4): still include headers for everything used
  directly in a given file — PCH is a build optimization, not a dependency contract. Files must
  compile correctly even if the PCH were removed.

## Questions

### Standard & Toolchain
1. **C++ standard?** (14 / 17 / 20 / 23) — becomes the "Code must compile with C++NN" rule.
2. **Compiler/toolchain target?** (MSVC / GCC / Clang / multiple — if multiple, which is primary for day-to-day dev)
3. **Build system?** (CMake / Bazel / Meson / plain Makefiles / other) — informs the shape of
   the Build & Test section, but the actual commands are always discovered/confirmed fresh
   (see `workflow-init`'s own notes on Build & Test), never templated here.
4. **Using a precompiled header (PCH)?** (yes/no) — if yes, the PCH static rule above applies
   and is worth stating explicitly in the project's `CLAUDE.md`, since it's a common source of
   "it compiled because of PCH order, not because I actually included what I use" bugs.

### Testing
5. **Test framework?** (googletest / Catch2 / doctest / none yet — if none yet, ask whether to
   default to googletest or leave the choice open)
6. **Test file convention?** (one file per class/module / one file per translation unit / other)
   — only worth asking if the project already has an established pattern to preserve; skip for
   a brand-new project and let it emerge.

### Dependencies
7. **Package/dependency management?** (vcpkg / Conan / CMake FetchContent / vendored / none)
8. **Any dependencies already known to be needed?** (e.g. a windowing/graphics library, a JSON
   library, a networking library) — free text, feeds into the project's own dependency list,
   not a fixed menu.

### Style
9. **Comment/documentation-block convention?** (Doxygen / plain comments / language-standard
   docstring-equivalent — C++ doesn't have one, so this usually resolves to "Doxygen or
   nothing") — the general "IDE-generated doc-comment blocks are acceptable" rule already
   lives in `WORKFLOW.md`; this question is only about which *style* the doc-comments should
   follow, if any.

---

## Gitignore Fragment

C++-specific entries `workflow-init`/`workflow-update` should add to the project's `.gitignore`
(workflow-universal entries — `context_dir/*`, `.claude/settings.local.json`, OS cruft, `.env`
patterns — are handled separately, not part of this template; see the skills' own instructions):

```
# Build
build/
cmake-build-*/
out/

# IDE
.vs/
.vscode/
.idea/
*.user
*.suo

# Compiled
*.o
*.obj
*.exe
*.dll
*.lib
*.a
*.so
*.dylib

# CMake
CMakeCache.txt
CMakeFiles/
cmake_install.cmake
Makefile
compile_commands.json
.cache/
```

Adjust if the build-system question (Q3) answered something other than CMake — the CMake block
above only applies when CMake is actually in use.
