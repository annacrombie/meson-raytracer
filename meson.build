project('ray', version: 'tracer', meson_version: '>=0.60.0')

dim = get_option('dim')

scale = 10000
dim_fp = dim * scale
scale_div_2 = scale / 2
three_halves = (scale / 2) + scale
v_empty = [0, 0, 0]
plane_y_fp = -2 * scale
ray_epsilon = 100
ray_dir = [0, 0, 0]
plane_color_1 = [10*222, 10*125, 10*484]
plane_color_2 = [(scale / 100)*45, (scale / 100)*45, (scale / 100)*45]
sphere_r = 2 * scale
sphere_r2 = (sphere_r * sphere_r) / scale
sphere_center = [scale, 0 * scale, 4 * scale]
sphere_color = [(scale / 100)*45, (scale / 100)*45, (scale / 100)*45]
light_pos = [-2 * scale, 4 * scale, 1 * scale]
light_color = [40*2851,40*7460,40*3984]
bg = [10*222, 10*125, 10*484]
retto = -1
s = []
sl = 0
pix = []
pc = 0

cmp = 0
ll = 4
_ = 0

p_plane_intersect = [
    '!set', 'pb', 'pc', _,
    'set', 'pl', 'l_plane_intersect', _,
    'vget', 'ray_dir_y', 'ray_dir', 1,
    'cmp', 'ray_dir_y', 0, _,
    'jne', 2*ll, _, _,
    'set', 'hit_t', -1, _,
    'ret', _, _, _,

    'set', 't', plane_y_fp, _,
    'vget', 'ray_origin_y', 'ray_origin', 1,
    '!-', 't', 'ray_origin_y', _,
    '!/', 't', 'ray_dir_y', _,

    'between', 't', 0, scale * 20,
    'jnz', 2*ll, _, _,
    'set', 'hit_t', -1, _,
    'ret', _, _, _,

    '!set', 'hit_t', 't', _,
    '!vcopy', 'v1', 'ray_dir', _,
    '!v*', 'v1', 't', _,
    '!v+', 'v1', 'ray_origin', _,
    '!vcopy', 'hit_point', 'v1', _,
    'vclear', 'hit_normal', _, _,
    'vset', 'hit_normal', 1, scale,
]
l_plane_intersect = p_plane_intersect.length()

p_sphere_intersect = [
    '!set', 'pb', 'pc', _,
    'set', 'pl', 'l_sphere_intersect', _,

    '!vcopy', 'oc', 'ray_origin', _,
    'v-', 'oc', sphere_center, _,
    '!v.v', 'rd_rd', 'ray_dir', 'ray_dir',
    '!v.v', 'half_b', 'oc', 'ray_dir',
    '!v.v', 'ac', 'oc', 'oc',
    '-', 'ac', sphere_r2, _,

    '!set', 'discrim', 'half_b', _,
    '!*', 'discrim', 'half_b', _,
    '!*', 'ac', 'rd_rd', _,
    '!-', 'discrim', 'ac', _,

    'cmp', 'discrim', 0, _,
    'jg', 2*ll, _, _,
    'set', 'hit_t', -1, _,
    'ret', _, _, 'sphere intersect 1',

    'sqrt', 'discrim', _, _,
    'set', 'minus_half_b', 0, _,
    '!-', 'minus_half_b', 'half_b', _,

    '!set', 't', 'minus_half_b', _,
    '!-', 't', 'discrim', _,
    '!/', 't', 'rd_rd', _,

    'cmp', 't', 0, _,
    'jle', 11*ll, _, _,

    '!set', 'hit_t', 't', _,
    '!vcopy', 'hit_point', 'ray_dir', _,
    '!v*', 'hit_point', 't', _,
    '!v+', 'hit_point', 'ray_origin', _,
    '!vcopy', 'hit_normal', 'hit_point', _,
    'v-', 'hit_normal', sphere_center, _,
    'v/', 'hit_normal', sphere_r, _,
    'ret', _, _, 'sphere intersect',

    '!set', 't', 'minus_half_b', _,
    '!+', 't', 'discrim', _,
    '!/', 't', 'rd_rd', _,

    'cmp', 't', 0, _,
    'jg', -13*ll, _, _,

    'set', 'hit_t', -1, _,
]
l_sphere_intersect = p_sphere_intersect.length()

p_light = [
    'vclear', 'lcol', _, _,
    '!vcopy', 'l', 'light_pos', _,
    '!v-', 'l', 'hit_point', _,
    '!vcopy', 'lnorm', 'l', _,
    'vnorm', 'lnorm', _, _,
    '!v.v', 'ndotl', 'hit_normal', 'lnorm',
    'cmp', 'ndotl', 0, _,
    'jl', 4*ll, _, _,
    '!vcopy', 'lcol', 'light_color', _,
    '!v*', 'lcol', 'ndotl', _,
    '!v.v', 'l2', 'l', 'l',
    '!v/', 'lcol', 'l2', _,
]

p_checker = [
    'vget', 'hit_point_x', 'hit_point', 0,
    'vget', 'hit_point_z', 'hit_point', 2,
    'fract', 'hit_point_x', _, _,
    'fract', 'hit_point_z', _, _,

    ] + p_light + [

    'cmp', 'hit_point_x', scale_div_2, _,
    'jl', 5*ll, _, _,
    'cmp', 'hit_point_z', scale_div_2, _,
    'jl', 3*ll, _, _,
    '!v+', 'rgb', 'plane_color_1', _,
    '!v*v', 'rgb', 'lcol', _,
    'ret', _, _, _,

    'cmp', 'hit_point_z', scale_div_2, _,
    'jg', 5*ll, _, _,
    'cmp', 'hit_point_x', scale_div_2, _,
    'jg', 3*ll, _, _,
    '!v+', 'rgb', 'plane_color_1', _,
    '!v*v', 'rgb', 'lcol', _,
    'ret', _, _, _,

    '!v+', 'rgb', 'plane_color_2', _,
    '!v*v', 'rgb', 'lcol', _,
]

p_ssurf = [
    '!vcopy', 'new_origin', 'hit_normal', _,
    'v*', 'new_origin', ray_epsilon, _,
    '!v+', 'new_origin', 'hit_point', _,

    '!v.v', 'scalar', 'hit_normal', 'ray_dir',
    '2*', 'scalar', _, _,
    '!vcopy', 'refl_a', 'hit_normal', _,
    '!v*', 'refl_a', 'scalar', _,
    '!vcopy', 'new_dir', 'ray_dir', _,
    '!v-', 'new_dir', 'refl_a', _,

    '!vcopy', 'ray_dir', 'new_dir', _,
    '!vcopy', 'ray_origin', 'new_origin', _,

    'push', 'hit_point', _, _,
    'push', 'hit_normal', _, _,
    'push', 'retto', _, _,
    '+', 'trace_depth', 1, _,
    '!set', 'retto', 'pc', _,
    '+', 'retto', 2*ll, _,
    '!jmp', 'pb_trace', _, _,
    'pop', 'retto', _, _,
    'pop', 'hit_normal', _, _,
    'pop', 'hit_point', _, _,

    ] + p_light + [
    '!v+', 'rgb', 'lcol', _,
    '!v*v', 'rgb', 'sphere_color', _,
]

p_trace = [
    '!set', 'pb_trace', 'pc', _,
    'cmp', 'trace_depth', 3, _,
    'jl', 1*ll, _, _,
    'ret', _, _, _,

    '!vcopy', 'rgb', 'bg', _,

    ] + p_sphere_intersect + [
    '!set', 'pb', 'pb_trace', _,
    'set', 'pl',  'l_trace', _,

    'cmp', 'hit_t', ray_epsilon, _,
    'jl', p_ssurf.length()+1*ll, _, _,
    ] + p_ssurf + [
    'ret', _, _, _,

    ] + p_plane_intersect + [
    '!set', 'pb', 'pb_trace', _,
    'set', 'pl',  'l_trace', _,

    'cmp', 'hit_t', ray_epsilon, _,
    'jg', 1*ll, _, _,
    'ret', _, _, _,
    ] + p_checker + [

    'ret', _, _, _,
]
l_trace = p_trace.length()

p_calc_dir_coord = [
    '/', 'fp_coord', dim_fp, _,
    '*', 'fp_coord', 2 * scale, _,
    'set', 'dir_coord', scale, _,
]

p_l1 = [
    'set', 'x', 0, _,
    '!set', 'l1', 'pc', _,

    'vclear', 'rgb', _, _,
    'vclear', 'ray_origin', _, _,

    '!fp', 'fp_coord', 'x', _,
    ] + p_calc_dir_coord + [
    '!-', 'fp_coord', 'dir_coord', _,
    '!vset', 'ray_dir', 0, 'fp_coord',
    '!fp', 'fp_coord', 'y', _,
    ] + p_calc_dir_coord + [
    '!-', 'dir_coord', 'fp_coord', _,
    '!vset', 'ray_dir', 1, 'dir_coord',
    'vset', 'ray_dir', 2, scale,

    'set', 'trace_depth', 0, _,
    ] + p_trace + [

    'v*', 'rgb', 255 * scale, _,
    'vtrunc', 'rgb', _, _,
    'addpix', 'rgb', _, _,

    '+', 'x', 1, _,
    'cmp', 'x', dim, _,
    '!jl', 'l1', _, _,
]

p_m = [
    'set', 'y', 0, _,
    '!set', 'l0', 'pc', _,
    '!print', 'y', _, _,

    ] + p_l1 + [

    '+', 'y', 1, _,
    'cmp', 'y', dim, _,
    '!jl', 'l0', _, _,
]

prog = p_m
prog_length = prog.length()
if prog_length / 4 * 4 != prog_length
    error('prog unaligned')
endif

# foreach inst : range(prog_length / 4)
#     inst = inst * 4
#     message([inst, prog[inst+0], prog[inst+1], prog[inst+2], prog[inst+3]])
# endforeach
# error('')

foreach _ : range(4294967295)
    f = prog[pc]
    pc += 1
    a = prog[pc]
    pc += 1
    b = prog[pc]
    pc += 1
    c = prog[pc]
    pc += 1

    # p({'pc': pc - 4, 'inst': [f, a, b, c]})

    if f == '!print'
        message(get_variable(a))
    elif f == 'vprint'
        va = get_variable(a)
        message('v: (@0@, @1@, @2@)'.format(va[0], va[1], va[2]))
    elif f == 'set'
        set_variable(a, b)
    elif f == '!set'
        set_variable(a, get_variable(b))
    elif f == '!fp'
        set_variable(a, get_variable(b) * scale)
    elif f == 'between'
        v1 = get_variable(a)
        if b < v1 and v1 < c
            cmp = 1
        else
            cmp = 0
        endif
    elif f == 'cmp'
        v1 = get_variable(a)
        if v1 < b
            cmp = -1
        elif v1 > b
            cmp = 1
        else
            cmp = 0
        endif
    elif f == 'jne'
        if cmp != 0
            pc += a
        endif
    elif f == 'jl'
        if cmp == -1
            pc += a
        endif
    elif f == 'jle'
        if cmp != 1
            pc += a
        endif
    elif f == '!jl'
        if cmp == -1
            pc = get_variable(a)
        endif
    elif f == 'jg' or f == 'jnz'
        if cmp == 1
            pc += a
        endif
    elif f == '!jmp'
        pc = get_variable(a)
    elif f == 'ret'
        if retto >= 0 and get_variable('pl') == 'l_trace'
            pc = retto
        else
            pc = get_variable('pb') + get_variable(get_variable('pl')) - ll
        endif
    elif f == '+'
        set_variable(a, get_variable(a) + b)
    elif f == '!+'
        set_variable(a, get_variable(a) + get_variable(b))
    elif f == '-'
        set_variable(a, get_variable(a) - b)
    elif f == '!-'
        set_variable(a, get_variable(a) - get_variable(b))
    elif f == '*'
        set_variable(a, (get_variable(a) * b) / scale)
    elif f == '2*'
        set_variable(a, get_variable(a) * 2)
    elif f == '!*'
        set_variable(a, (get_variable(a) * get_variable(b)) / scale)
    elif f == '/'
        set_variable(a, (get_variable(a) * scale) / b)
    elif f == '!/'
        set_variable(a, (get_variable(a) * scale) / get_variable(b))
    elif f == 'fract'
        nv = get_variable(a) % scale
        if nv < 0
            nv += scale
        endif

        set_variable(a, nv)
    elif f == 'trunc'
        set_variable(a, get_variable(a) / scale)
    elif f == '%'
        set_variable(a, get_variable(a) % b)
    elif f == 'sqrt'
        va = get_variable(a)
        if va < 0
            error('sqrt negative')
        endif

        guess = va / 2
        foreach _ : range(5)
            if guess == 0
                break
            endif

            sqrt_tmp = (va * scale) / guess
            sqrt_tmp += guess
            guess = sqrt_tmp / 2
        endforeach

        set_variable(a, guess)
    elif f == '!v+'
        va = get_variable(a)
        vb = get_variable(b)
        set_variable(a, [va[0] + vb[0], va[1] + vb[1], va[2] + vb[2]])
    elif f == 'v-'
        va = get_variable(a)
        set_variable(a, [va[0] - b[0], va[1] - b[1], va[2] - b[2]])
    elif f == '!v-'
        va = get_variable(a)
        vb = get_variable(b)
        set_variable(a, [va[0] - vb[0], va[1] - vb[1], va[2] - vb[2]])
    elif f == 'v*'
        va = get_variable(a)
        set_variable(a, [(va[0] * b) / scale, (va[1] * b) / scale, (va[2] * b) / scale])
    elif f == '!v*'
        va = get_variable(a)
        fb = get_variable(b)
        set_variable(a, [(va[0] * fb) / scale, (va[1] * fb) / scale, (va[2] * fb) / scale])
    elif f == 'v/'
        va = get_variable(a)
        set_variable(a, [(va[0] * scale) / b, (va[1] * scale) / b, (va[2] * scale) / b])
    elif f == '!v/'
        va = get_variable(a)
        fb = get_variable(b)
        set_variable(a, [(va[0] * scale) / fb, (va[1] * scale) / fb, (va[2] * scale) / fb])
    elif f == '!v*v'
        va = get_variable(a)
        vb = get_variable(b)
        set_variable(a, [(va[0] * vb[0]) / scale, (va[1] * vb[1]) / scale, (va[2] * vb[2]) / scale])
    elif f == '!v.v'
        vb = get_variable(b)
        vc = get_variable(c)
        set_variable(a, (vb[0] * vc[0]) / scale + (vb[1] * vc[1]) / scale + (vb[2] * vc[2]) / scale)
    elif f == 'vnorm'
        va = get_variable(a)
        dot = (va[0] * va[0]) / scale + (va[1] * va[1]) / scale + (va[2] * va[2]) / scale

        dot2 = dot / 2
        guess = (scale * scale) / dot
        foreach _ : range(6)
            rsq_tmp = (guess * guess) / scale
            rsq_tmp = (rsq_tmp * dot2) / scale
            rsq_tmp = three_halves - rsq_tmp
            guess = (rsq_tmp * guess) / scale
        endforeach

        fb = guess
        set_variable(a, [(va[0] * fb) / scale, (va[1] * fb) / scale, (va[2] * fb) / scale])
    elif f == 'vtrunc'
        va = get_variable(a)
        set_variable(a, [va[0] / scale, va[1] / scale, va[2] / scale])
    elif f == 'vclear'
        set_variable(a, v_empty)
    elif f == '!vcopy'
        set_variable(a, get_variable(b))
    elif f == 'vset'
        old_v = get_variable(a)
        new_v = []
        foreach vi : range(3)
            if vi == b
                new_v += c
            else
                new_v += old_v[vi]
            endif
        endforeach

        set_variable(a, new_v)
    elif f == '!vset'
        old_v = get_variable(a)
        new_v = []
        foreach vi : range(3)
            if vi == b
                new_v += get_variable(c)
            else
                new_v += old_v[vi]
            endif
        endforeach

        set_variable(a, new_v)
    elif f == 'vget'
        set_variable(a, get_variable(b)[c])
    elif f == 'addpix'
        va = get_variable(a)
        pix += [va[0].to_string(), va[1].to_string(), va[2].to_string()]
    elif f == 'push'
        s += [get_variable(a)]
        sl += 1
    elif f == 'pop'
        i = 0
        ns = []
        nv = '?'
        foreach v : s
            i += 1
            if i == sl
                nv = v
                break
            endif

            ns += [v]
        endforeach
        s = ns
        sl = sl - 1
        set_variable(a, nv)
    else
        error(f'unknown function @f@')
    endif

    if pc >= prog_length
        break
    endif
endforeach

configure_file(input: 'scene.ppm.in',
    output: 'scene.ppm',
    configuration: {
        'dim': dim,
        'scene': ' '.join(pix)
    }
)
